---
name: api-test-analysis
description: Procedimento para analisar contratos, respostas e cobertura de teste de APIs fictícias a partir de especificação e amostras sintéticas fornecidas, sem nenhuma chamada externa. Use para avaliar risco e lacunas na camada de serviço.
---

# Skill: api-test-analysis

Procedimento reutilizável de análise da camada de API sob a ótica de QA. Trabalha
sobre o material fornecido no pedido — **nenhuma requisição real é feita, em
nenhum ambiente**.

## Quando usar

- Avaliar se um contrato de API está coerente e testável.
- Encontrar lacunas de cobertura em cenários de API.
- Entender se uma falha observada na interface tem origem na camada de serviço.

## Quando não usar

- É preciso observar a aplicação em execução → `execute-e2e` ou `agent-browser`.
- O pedido é diagnosticar um pipeline que falhou → `ci-failure-triage`.

## Pré-condições

1. Especificação fictícia disponível (operações, esquemas, códigos de resposta).
2. Amostras **sintéticas ou anonimizadas** de requisição e resposta.
3. Nenhum dado real: sem documento, e-mail, telefone, endereço, valor
   contratual, token ou chave. Encontrou? Interrompa, avise e peça substituição
   por dado fictício.

## Procedimento

1. **Mapear a superfície**: liste as operações, o que cada uma faz em uma frase,
   e o que é obrigatório em cada entrada.
2. **Conferir coerência do contrato**, operação por operação:
   - campo obrigatório na especificação aparece na amostra?
   - tipo declarado corresponde ao tipo da amostra?
   - códigos de resposta documentados cobrem sucesso, erro de entrada, falta de
     permissão e indisponibilidade?
   - o formato de erro é o mesmo em todas as operações?
3. **Levantar os limites**: campo vazio, valor no limite, valor acima do limite,
   caractere inesperado, ausência de campo opcional, repetição de requisição.
4. **Avaliar cobertura**: para cada operação, existe cenário de sucesso? de erro
   de entrada? de falta de permissão? Marque o que falta.
5. **Classificar cada achado** em exatamente uma categoria:
   - `divergencia-de-contrato` — especificação e amostra discordam;
   - `lacuna-de-especificacao` — o comportamento não está definido;
   - `lacuna-de-cobertura` — o comportamento está definido, mas não há cenário;
   - `risco` — funciona como documentado, mas o desenho convida ao erro.
6. **Registrar a evidência de cada achado**: o trecho da especificação e o
   trecho da amostra que sustentam a afirmação, citados como referência.
7. **Encaminhar**:
   - `divergencia-de-contrato` com evidência suficiente → `bug-draft` em
     rascunho;
   - `lacuna-de-cobertura` → `scenario-draft` sugerido;
   - `lacuna-de-especificacao` e `risco` → pergunta registrada para o time, sem
     veredito.

## Formato da saída

Responda em JSON compatível com o contrato pedido no fluxo:
`harness/contracts/bug-draft.schema.json` ou
`harness/contracts/scenario-draft.schema.json`. Sem contrato aplicável, use o
relatório em texto com um achado por bloco.

## Proibições

- Nenhuma chamada de rede. Nenhuma requisição, nem em laboratório.
- Não preencha lacuna de especificação com suposição apresentada como fato.
- Não afirme defeito sem evidência citável — ver
  `harness/policies/evidence-policy.md`.
- Não reproduza nada que pareça segredo ou dado pessoal, nem para ilustrar.
- Não proponha correção de implementação: aponte sintoma e risco.

## Declaração de incerteza

Todo achado carrega uma confiança entre `0` e `1`. Abaixo de `0.6`, o achado é
apresentado como **hipótese** e vai para revisão humana com as alternativas
explicitadas.

## Critérios de parada

- Aparece dado sensível no material → pare e avise.
- Especificação e amostra se contradizem sem referência decisória → pare e
  pergunte.
- Achado indica exposição de dado → pare, não reproduza, escale com prioridade.
