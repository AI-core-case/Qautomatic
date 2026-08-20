# Fluxo: triage-ci-failure

Triagem de uma falha de pipeline fictícia, até classificação com causa provável e
confiança.

- **Coordenador:** `qa-leader`
- **Executor:** `ci-triage`
- **Skill:** `ci-failure-triage`
- **Contrato de saída:** `harness/contracts/triage-result.schema.json`

## Objetivo

Dizer **em qual camada** a falha está, qual a causa mais provável, com que
confiança, e qual a próxima ação — ou dizer com clareza que a evidência não
permite decidir.

## Entrada

| Campo | Obrigatório | Observação |
|---|---|---|
| Registro de execução do pipeline | sim | sanitizado |
| `test-result` dos cenários que falharam | quando existirem | saída de `run-e2e` |
| Evidências | quando existirem | `.qa-runs/artifacts/` |
| Identificação do ambiente | sim | ambiente fictício |

Log com aparência de credencial, token ou dado pessoal é mascarado antes de
entrar em qualquer citação.

## Classificação obrigatória

Exatamente um destes cinco valores:

- **`interface-e2e`** — a aplicação se comportou de forma diferente do esperado
  na interface: elemento ausente, estado divergente, texto exibido diferente.
- **`api`** — falha na camada de serviço: código de resposta inesperado, corpo
  fora do contrato, campo ausente.
- **`automacao`** — o cenário ou o código de teste está errado, frágil ou
  desatualizado; a aplicação está correta: espera fixa por tempo, seletor
  frágil, dependência de ordem ou de dado preexistente, esperado desatualizado.
- **`ambiente`** — indisponibilidade, lentidão, dependência fora do ar, recurso
  esgotado; tipicamente falha antes do primeiro passo do cenário.
- **`configuracao`** — parâmetro divergente entre o esperado e o que o pipeline
  usou: variável ausente, versão diferente, permissão insuficiente, apontamento
  errado.

Nenhuma se sustenta? `inconclusivo`, com confiança baixa e a lista do que falta
para decidir.

## Etapas

1. **Delimitar o sintoma** em uma frase.
2. **Reconstruir a linha do tempo** até o ponto exato da parada.
3. **Separar falha de preparação de falha de execução** — preparação aponta para
   `ambiente` ou `configuracao`.
4. **Verificar causa comum** — falha idêntica em todos os cenários raramente é
   defeito de produto.
5. **Consultar `app-memory/known-issues/`** — reincidência é resposta, não
   recomeço.
6. **Formular pelo menos duas hipóteses concorrentes**, com a evidência que
   confirma e a que derruba cada uma.
7. **Confrontar com a evidência disponível** — só isso decide.
8. **Classificar** e **atribuir confiança** de `0` a `1`, pela evidência, não
   pela plausibilidade da narrativa.
9. **Definir a próxima ação**: uma, concreta, para uma pessoa.
10. **Gravar** o `triage-result` com `revisao_humana_obrigatoria: true`.

## Saída

Um `triage-result` com classificação, causa provável, confiança, evidências,
próxima ação e revisão humana obrigatória. Quando há defeito com evidência
suficiente, acompanha um `bug-draft` em rascunho — que **não** vira ticket sem
aprovação humana.

## Evidências

No mínimo uma evidência verificável por classificação: caminho de artefato em
`.qa-runs/artifacts/` ou referência a trecho identificado do registro de
execução. Sem isso, a classificação é `inconclusivo`. Ver
`harness/policies/evidence-policy.md`.

## Critérios de parada

- Confiança abaixo de `0.6` → entrega as hipóteses lado a lado e escala, sem
  escolher.
- Duas classificações igualmente sustentadas → escala.
- Indício de perda de dado, indisponibilidade ampla ou exposição de segredo →
  interrompe o resto da triagem e escala com prioridade máxima.
- Sintoma já registrado em `known-issues` → aponta a reincidência e escala.

## Proibições

Reexecutar o pipeline para ver se passa; corrigir código ou cenário; classificar
sem evidência; chamar de instabilidade o que não foi comprovado; abrir, editar ou
fechar ticket externo; citar segredo, ainda que parcialmente.
