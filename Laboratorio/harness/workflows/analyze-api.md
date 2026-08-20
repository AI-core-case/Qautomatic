# Fluxo: analyze-api

Análise da camada de API a partir de especificação e amostras fornecidas.

- **Coordenador:** `qa-leader`
- **Executor:** `api-test-analyst`
- **Skill:** `api-test-analysis`
- **Contratos de saída:** `harness/contracts/bug-draft.schema.json`,
  `harness/contracts/scenario-draft.schema.json`

## Objetivo

Avaliar coerência de contrato, cobertura de teste e risco na camada de serviço —
inclusive o que a interface não revela.

**Nenhuma requisição real é feita.** O fluxo opera exclusivamente sobre o
material do pedido.

## Entrada

| Campo | Obrigatório | Observação |
|---|---|---|
| Especificação fictícia | sim | operações, esquemas, códigos de resposta |
| Amostras de requisição/resposta | sim | sintéticas ou anonimizadas |
| Cenários de API existentes | não | para avaliar cobertura |
| Escopo | sim | quais operações analisar |

Material com aparência de dado real (documento, e-mail, telefone, valor
contratual, token) interrompe o fluxo antes da primeira etapa.

## Etapas

1. **Higienizar a entrada** — confirmar que tudo é fictício. Encontrou dado real?
   Pare, avise, peça substituição.
2. **Mapear a superfície** — operações, propósito em uma frase, entradas
   obrigatórias.
3. **Conferir coerência do contrato** — campos, tipos, códigos de resposta,
   uniformidade do formato de erro.
4. **Levantar limites** — vazio, limite, acima do limite, caractere inesperado,
   ausência de opcional, repetição de requisição.
5. **Avaliar cobertura** — sucesso, erro de entrada e falta de permissão por
   operação.
6. **Classificar cada achado** em `divergencia-de-contrato`,
   `lacuna-de-especificacao`, `lacuna-de-cobertura` ou `risco`.
7. **Atribuir confiança** de `0` a `1` por achado.
8. **Encaminhar** — divergência com evidência suficiente vira `bug-draft` em
   rascunho; lacuna de cobertura vira `scenario-draft`; lacuna de especificação e
   risco viram pergunta registrada para o time.

## Saída

- Relatório de achados: operação, categoria, evidência, risco, confiança.
- `bug-draft` em rascunho, quando cabível — sempre pendente de aprovação humana.
- `scenario-draft` sugerido para cada lacuna de cobertura.
- Lista de perguntas abertas para o time.

## Evidências

Cada achado cita **a referência na especificação** e **a referência na amostra**
que o sustentam. Achado sem as duas referências é hipótese, não achado — e vai
rotulado como hipótese.

Nada que pareça segredo ou dado pessoal é reproduzido, nem para ilustrar.

## Critérios de parada

- Dado sensível na entrada → para imediatamente.
- Especificação e amostra se contradizem sem referência decisória → pergunta,
  não decide.
- Achado indica exposição de dado → para, não reproduz, escala com prioridade.
- Severidade sugerida alta ou crítica → escala antes de qualquer
  encaminhamento.
- Confiança abaixo de `0.6` → entrega como hipótese, com as alternativas.

## Proibições

Qualquer chamada de rede; usar dado real; preencher lacuna de especificação com
suposição apresentada como fato; afirmar defeito sem evidência citável; propor
correção de implementação; abrir ticket externo.
