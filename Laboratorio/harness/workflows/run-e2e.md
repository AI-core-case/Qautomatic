# Fluxo: run-e2e

Execução de uma suíte E2E/UI determinística em ambiente autorizado.

- **Coordenador:** `qa-leader`
- **Executor:** `e2e-executor`
- **Skill:** `execute-e2e`
- **Contrato de saída:** `harness/contracts/test-result.schema.json`

## Objetivo

Responder a uma pergunta fechada: *os cenários desta suíte passam, neste
ambiente, agora?* Um resultado por cenário, com evidência.

Este fluxo não diagnostica causa. Falha aqui alimenta `triage-ci-failure`.

## Entrada

| Campo | Obrigatório | Exemplo fictício |
|---|---|---|
| Conjunto de cenários | sim | `suite-exemplo-01` |
| Ambiente-alvo | sim | `ambiente-laboratorio` |
| Aprovação humana | só para produção | referência da aprovação |
| Limite de reexecução | não (padrão: 1) | `1` |
| Dado de teste | quando o cenário exigir | usuário fictício de laboratório |

Sem ambiente-alvo, o fluxo não começa.

## Etapas

1. **Validar entrada** — escopo, ambiente, dado de teste fictício, permissões de
   escrita conforme `harness/policies/write-permissions-policy.md`. Falhou?
   `bloqueado` com motivo.
2. **Abrir a execução** — registro em `.qa-runs/pending/` com escopo, ambiente,
   data e hora.
3. **Executar cenário por cenário**, seguindo `execute-e2e`: passos como
   escritos, comparação com o esperado sem reinterpretação, coleta de evidência.
4. **Consolidar** — um `test-result` por cenário, mais o resumo em até cinco
   linhas.
5. **Fechar a execução** — mover o registro para `.qa-runs/completed/` ou
   `.qa-runs/failed/`.
6. **Encaminhar** — cada `falhou` ou `instavel` entra na lista de entrada de
   `triage-ci-failure`; nada vira bug aqui.

## Saída

- Um `test-result` por cenário, validado contra o contrato.
- Resumo: quantos passaram, falharam, ficaram bloqueados, precisam de olho
  humano.
- Lista de falhas encaminhadas para triagem.

## Evidências

Para todo cenário com `status: falhou`, no mínimo:

- captura de tela do estado no momento da falha, ou motivo registrado de por que
  a captura não foi possível;
- trecho do registro de execução correspondente;
- descrição em uma frase do esperado e do observado.

Tudo em `.qa-runs/artifacts/`, referenciado por caminho relativo dentro do
`test-result`. Ver `harness/policies/evidence-policy.md`.

## Critérios de parada

- Ambiente indisponível durante a execução → `bloqueado`, escala.
- Credencial ou dado ausente → para, escala, não fabrica.
- Indício de perda de dado ou de falha de segurança → interrompe a suíte inteira
  e escala com prioridade.
- Ambiente se revela produção sem aprovação → aborta.
- Volume de falhas indica ambiente errado, não aplicação → para e escala.
- Limite de reexecução atingido → registra falha real.

## Proibições

Alterar, desativar ou ignorar cenário que falhou; afrouxar verificação para
produzir verde; executar ação irreversível; inventar evidência; abrir ticket
externo.
