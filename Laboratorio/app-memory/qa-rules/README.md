# app-memory/qa-rules/

Memória de **regras de QA**: as convenções que este laboratório adota para
escrever, priorizar e executar teste.

Existe para que duas execuções separadas por semanas produzam material com a
mesma forma, e para que uma pessoa que chega depois entenda o critério em vez de
adivinhá-lo.

## O que pode ser registrado aqui

- Convenções de escrita de cenário: estrutura, nível de detalhe, o que vai em
  pré-condição e o que vai em passo.
- Critérios de priorização: o que faz um cenário ser `critica` em vez de `alta`.
- Definição de pronto para um cenário: quando ele pode entrar na suíte.
- Política de reexecução e o que caracteriza instabilidade comprovada.
- Nomenclatura de identificadores genéricos.
- Regras de cobertura mínima por tipo de mudança.
- Convenções de armazenamento de evidência.

## O que não pode ser registrado aqui

- Dado de pessoa, cliente ou contrato.
- Credencial, token ou segredo de qualquer tipo.
- URL, hostname ou endpoint de ambiente real.
- Nome de produto, cliente ou fornecedor real.

## Como registrar

Uma regra por bloco, com o critério objetivo primeiro e o motivo depois. Regra
sem critério verificável não é regra — é preferência, e preferência não resolve
disputa.

Quando uma regra tem exceção, a exceção fica registrada junto, com a condição que
a habilita.

## Relação com as políticas do harness

`harness/policies/` define o que é **obrigatório e inegociável**. Este diretório
guarda o que é **convenção deste laboratório**. Em caso de conflito, a política
do harness prevalece, e o conflito deve ser escalado.

## Quem escreve

Escrita aqui **exige aprovação humana** — ver
`harness/policies/human-approval-policy.md`.
