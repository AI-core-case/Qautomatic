---
name: execute-e2e
description: Procedimento operacional para executar uma suíte E2E/UI determinística em ambiente autorizado, coletar evidências e devolver o resultado no contrato test-result. Use quando o pedido for executar cenários que já existem.
---

# Skill: execute-e2e

Procedimento reutilizável para execução de cenários E2E/UI **já escritos**. Esta
skill não escreve cenário e não julga produto: ela executa, observa e registra.

## Quando usar

- O pedido é "rodar a suíte", "executar os cenários de X", "verificar se a
  regressão passa".
- Os cenários já existem e são determinísticos.

## Quando não usar

- Não há cenário escrito → `scenario-reviewer` primeiro.
- O pedido é exploratório, sem roteiro → skill `agent-browser`.
- O pedido é diagnosticar uma falha que já aconteceu → skill
  `ci-failure-triage`.

## Pré-condições

Confirme, na ordem, antes de qualquer execução:

1. **Ambiente autorizado identificado.** Sem ambiente informado, pare e
   pergunte. Nunca assuma o padrão.
2. **Ambiente não é produção** — ou, se for, existe aprovação humana explícita
   anexada ao pedido.
3. **Escopo definido**: quais cenários, em que ordem.
4. **Destino das evidências existe**: `.qa-runs/artifacts/`.
5. **Dado de teste é fictício.** Qualquer credencial ou dado que pareça real
   interrompe o procedimento.

Falhou uma pré-condição? O resultado é `status: bloqueado` com o motivo. Não
prossiga "só para tentar".

## Procedimento

1. **Registrar a abertura da execução** em `.qa-runs/pending/`: escopo,
   ambiente, data e hora.
2. **Para cada cenário, na ordem definida:**
   1. registrar o cenário que começou;
   2. executar os passos exatamente como escritos — sem improvisar caminho
      alternativo quando um passo falha;
   3. comparar o observado com o resultado esperado do cenário, sem
      reinterpretar o esperado;
   4. coletar a evidência prevista (captura de tela do estado final; registro de
      execução; estado observado descrito em uma frase);
   5. atribuir o `status`: `passou`, `falhou`, `instavel`, `bloqueado` ou
      `inconclusivo`;
   6. gravar um `test-result` conforme
      `harness/contracts/test-result.schema.json`.
3. **Ao terminar**, mover o registro de `.qa-runs/pending/` para
   `.qa-runs/completed/` — ou para `.qa-runs/failed/` se a execução foi
   interrompida.
4. **Resumir** em no máximo cinco linhas: quantos passaram, quantos falharam, o
   que ficou bloqueado e o que precisa de olho humano.

## Reexecução

Uma reexecução, no máximo, e apenas quando a falha aconteceu **antes** do
primeiro passo do cenário (preparação de ambiente, carga inicial, dependência
não disponível). Falha dentro do cenário é falha: registre.

Duas falhas seguidas no mesmo cenário são falha real, sempre.

## Proibições

- Não altere, desative, ignore nem marque como pendente um cenário que falhou.
- Não afrouxe uma verificação para produzir verde.
- Não execute ação irreversível: exclusão de dado, mudança administrativa,
  pagamento, envio de mensagem.
- Não invente evidência. Captura que falhou é registrada como captura que
  falhou.
- Não conclua que "provavelmente passa". Sem observação, o status é
  `inconclusivo`.

## Critérios de parada

Pare imediatamente e escale quando:

- o ambiente ficar indisponível no meio da execução;
- um cenário exigir credencial ou dado não fornecido;
- surgir indício de perda de dado ou falha de segurança;
- o ambiente-alvo se revelar produção sem aprovação;
- a quantidade de falhas indicar que o ambiente está errado, não a aplicação.

## Evidência mínima

Ver `harness/policies/evidence-policy.md`. Resumo: nenhum `status: falhou` sai
desta skill sem pelo menos uma evidência verificável referenciada por caminho.
