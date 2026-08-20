---
name: ci-failure-triage
description: Procedimento para triar uma falha de pipeline fictícia, classificando-a em interface/E2E, API, automação, ambiente ou configuração, com causa provável, confiança e evidências. Use quando um pipeline falhou e a causa ainda não é conhecida.
---

# Skill: ci-failure-triage

Procedimento reutilizável de triagem. O produto é um diagnóstico honesto —
inclusive quando o diagnóstico honesto é "não sei ainda, e por isto".

## Quando usar

- Um pipeline fictício falhou e a causa não está estabelecida.
- Vários cenários falharam juntos e é preciso saber se a causa é comum.

## Quando não usar

- A causa já é conhecida e o pedido é corrigir → não é triagem.
- O pedido é executar teste → `execute-e2e`.

## Pré-condições

1. Registro de execução disponível, ainda que parcial.
2. Resultados dos cenários que falharam (`test-result`), quando existirem.
3. Acesso a `.qa-runs/artifacts/` para as evidências.
4. Log **sanitizado**: qualquer trecho com aparência de credencial, token ou dado
   pessoal é mascarado antes de entrar em qualquer citação.

## As cinco classificações

Escolha **exatamente uma**:

| Classificação | Significa | Sinais típicos |
|---|---|---|
| `interface-e2e` | A aplicação se comportou de forma diferente do esperado na interface | Elemento esperado não apareceu; estado da tela divergiu; texto exibido diferente do esperado |
| `api` | A falha está na camada de serviço | Código de resposta inesperado; corpo fora do contrato; campo ausente na resposta |
| `automacao` | O cenário ou o código de teste está errado, frágil ou desatualizado; a aplicação está correta | Espera fixa por tempo; seletor frágil; cenário depende de ordem ou de dado preexistente; o esperado do cenário está desatualizado |
| `ambiente` | Indisponibilidade ou recurso esgotado | Dependência fora do ar; tempo esgotado na preparação; falha antes do primeiro passo do cenário |
| `configuracao` | Parâmetro divergente entre o esperado e o que o pipeline usou | Variável ausente; versão diferente; permissão insuficiente; parâmetro apontando para o lugar errado |

Nenhuma se sustenta com a evidência disponível? A classificação é
`inconclusivo`, com confiança baixa e a lista do que falta para decidir.

## Procedimento

1. **Delimitar o sintoma** em uma frase: o que falhou, em que ponto, em qual
   cenário.
2. **Estabelecer a linha do tempo**: o que aconteceu antes da falha; onde
   exatamente o pipeline parou.
3. **Separar falha de preparação de falha de execução.** Falha antes do primeiro
   passo do cenário aponta para `ambiente` ou `configuracao`.
4. **Verificar se é comum**: todos os cenários falharam no mesmo ponto? Causa
   comum tende a `ambiente` ou `configuracao`, não a defeito de produto.
5. **Consultar `app-memory/known-issues/`**: o sintoma é reincidente? Se sim,
   aponte a reincidência em vez de refazer a análise do zero.
6. **Formular hipóteses concorrentes** — pelo menos duas — e, para cada uma,
   qual evidência a confirmaria e qual a derrubaria.
7. **Confrontar com a evidência que existe.** Só isso decide.
8. **Atribuir a confiança** entre `0` e `1`, com base na evidência, não na
   plausibilidade da história.
9. **Definir a próxima ação**: uma ação, concreta, para uma pessoa.
10. **Gravar** o `triage-result` conforme
    `harness/contracts/triage-result.schema.json`, com
    `revisao_humana_obrigatoria: true`.

## Regra sobre instabilidade

"Instável" não é explicação de conveniência. Só use quando o **mesmo** cenário,
no **mesmo** estado, produziu resultados diferentes, e isso está registrado.
Suspeita não comprovada reduz a confiança — não vira rótulo.

## Proibições

- Não reexecute o pipeline para ver se passa.
- Não corrija cenário nem código: recomende.
- Não classifique sem evidência.
- Não abra, edite ou feche ticket externo — ver
  `harness/policies/human-approval-policy.md`.
- Não cite segredo, nem parcialmente, nem como exemplo.

## Critérios de parada

- Confiança abaixo de `0.6` → entregue as hipóteses concorrentes lado a lado e
  escale, sem escolher.
- Duas classificações igualmente sustentadas → escale.
- Indício de perda de dado, indisponibilidade ampla ou exposição de segredo →
  pare o resto da triagem e escale com prioridade máxima.
