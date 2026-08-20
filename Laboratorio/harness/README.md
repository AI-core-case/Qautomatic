# harness/

O harness é a parte do laboratório que define **como** o trabalho de QA é feito:
os fluxos, as regras, os formatos de saída e os prompts.

Nada aqui executa por conta própria. Estes arquivos são a instrução que os
agentes de `.claude/agents/` seguem quando são acionados.

## Os quatro diretórios

### `workflows/`

Um arquivo por fluxo de trabalho. Cada um descreve objetivo, entrada, etapas,
saída, evidências e critérios de parada.

| Fluxo | Responde a |
|---|---|
| `run-e2e.md` | Os cenários desta suíte passam, neste ambiente, agora? |
| `analyze-api.md` | O contrato da camada de serviço é coerente e está coberto? |
| `triage-ci-failure.md` | Em qual camada está esta falha, e com que confiança? |
| `generate-scenarios.md` | Este comportamento está coberto por cenário legível e determinístico? |
| `browser-exploration.md` | O que ninguém pensou em cobrir? |

### `policies/`

Regras que valem para todos os fluxos. Quando um fluxo e uma política
divergirem, a política decide.

| Política | Define |
|---|---|
| `evidence-policy.md` | O que conta como evidência e qual é o mínimo por tipo de conclusão |
| `bug-classification-policy.md` | Quando um sintoma pode ser chamado de bug e como a severidade é sugerida |
| `human-approval-policy.md` | O que o harness nunca faz sozinho |
| `write-permissions-policy.md` | Onde é possível escrever e quais ações são bloqueadas sempre |
| `browser-safety-policy.md` | Limites de toda interação com interface |

### `contracts/`

JSON Schemas (draft-07) que definem o formato de cada saída estruturada. Um
agente que não consegue preencher os campos obrigatórios de um contrato não tem
uma conclusão — tem uma pendência.

| Contrato | Produzido por |
|---|---|
| `test-result.schema.json` | `e2e-executor` |
| `triage-result.schema.json` | `ci-triage` |
| `bug-draft.schema.json` | `ci-triage`, `api-test-analyst` |
| `scenario-draft.schema.json` | `scenario-reviewer` |
| `browser-session.schema.json` | `browser-explorer` |

### `prompts/`

Prompts reutilizáveis, um por tipo de raciocínio. Todos exigem resposta em JSON
compatível com o contrato correspondente e todos instruem a declarar incerteza,
não inventar evidência e pedir revisão humana quando for o caso.

## Como as peças se ligam

```
pedido
  │
  ▼
qa-leader ──── escolhe o fluxo em workflows/
  │
  ├─► agente especialista em .claude/agents/
  │        │
  │        ├─ segue a skill em .claude/skills/
  │        ├─ obedece às regras em policies/
  │        ├─ raciocina com o prompt em prompts/
  │        └─ escreve a saída no formato de contracts/
  │
  ▼
resultado gravado em .qa-runs/  ──►  revisão humana
```

## Invariantes

Valem em todo fluxo, sem exceção:

1. **Escrita apenas em `.qa-runs/`** sem aprovação humana nomeando o caminho.
2. **Nenhum ticket externo** é criado, editado ou fechado pelo harness.
3. **Nenhuma ação irreversível**: exclusão de dado, mudança administrativa,
   pagamento, envio de mensagem, exposição de segredo.
4. **Nenhuma conclusão sem evidência.** Sem evidência mínima, a saída é
   `inconclusivo` ou hipótese declarada.
5. **Nenhum dado real.** Só dados fictícios e placeholders.
6. **Produção exige aprovação humana explícita**, com escopo e janela.
