# app-memory/test-knowledge/

Memória de **conhecimento de teste**: o que este laboratório aprendeu sobre onde
o risco mora e o que costuma escapar.

Existe porque a parte mais difícil de QA não é executar — é saber onde olhar.
Esse conhecimento normalmente vive na cabeça de quem já testou; aqui ele fica
escrito.

## O que pode ser registrado aqui

- Áreas historicamente frágeis, com o motivo pelo qual são frágeis.
- Lacunas de cobertura conhecidas e ainda não endereçadas.
- Padrões de falha recorrentes por camada (o que costuma quebrar na interface, na
  API, na automação, no ambiente).
- Armadilhas de automação identificadas: o que torna um cenário frágil neste
  contexto.
- Heurísticas de exploração que já renderam achado.
- Limites conhecidos das ferramentas de teste.
- Relação entre tipo de mudança e cobertura recomendada.

## O que não pode ser registrado aqui

- Dado de pessoa, cliente ou contrato.
- Credencial, token ou segredo.
- URL, hostname ou endpoint real.
- Trecho de log de produção.
- Nome de produto, cliente ou fornecedor real.

## Como registrar

Um tema por arquivo. Cada item diz **o que observar** e **por quê** — a
observação sem o motivo envelhece e ninguém sabe se ainda vale.

Item que deixou de valer é removido, não acumulado. Memória de teste que ninguém
confia é pior do que memória vazia.

## Diferença em relação a `known-issues/`

- `known-issues/` guarda **sintomas específicos já vistos**.
- Este diretório guarda **padrões e risco** — o que ajuda a encontrar o que ainda
  não foi visto.

## Quem escreve

Escrita aqui **exige aprovação humana** — ver
`harness/policies/human-approval-policy.md`.
