# app-memory/known-issues/

Memória de **problemas conhecidos**: sintomas já observados, já analisados e cuja
natureza já foi estabelecida.

Existe para evitar o desperdício mais comum de um harness de QA: reanalisar do
zero, a cada execução, a mesma falha que já se sabe de onde vem.

O fluxo `triage-ci-failure` consulta este diretório antes de formular hipóteses.
Sintoma reincidente é resposta, não recomeço.

## O que pode ser registrado aqui

- Sintoma observável, descrito como aparece — não como se explica.
- Classificação estabelecida (interface/E2E, API, automação, ambiente,
  configuração).
- Causa conhecida, quando confirmada.
- Como reconhecer a reincidência: a marca no registro de execução ou na tela que
  identifica este caso e não outro.
- Contorno aplicável, quando existir.
- Situação atual: em aberto, contornado, resolvido.
- Referência ao registro externo, **apenas como identificador genérico**.

## O que não pode ser registrado aqui

- Trecho de log com credencial, token ou chave, ainda que mascarado por
  aproximação.
- Dado de pessoa, cliente ou contrato.
- Captura de tela contendo dado sensível.
- Nome de sistema, fornecedor ou cliente real.
- Detalhe de vulnerabilidade explorável.

Sintoma de segurança entra aqui apenas como categoria e situação — nunca com o
caminho para reproduzir.

## Como registrar

Um arquivo por problema. Comece pelo sintoma como ele aparece, porque é isso que
um agente vai comparar. A causa vem depois.

Todo item registrado diz **como reconhecer que é este caso** — sem esse
critério, o registro produz falsos positivos e piora a triagem em vez de
melhorá-la.

## Quem escreve

Escrita aqui **exige aprovação humana** — ver
`harness/policies/human-approval-policy.md`. Agentes propõem; pessoas confirmam.
