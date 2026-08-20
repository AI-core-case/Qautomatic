# Laboratório — QA Agent Harness (fictício)

Estrutura de demonstração de um **QA Agent Harness**: o arcabouço que permite a
um conjunto de agentes fazer trabalho de QA de forma previsível, auditável e
segura.

Tudo aqui é fictício. Não há teste real, integração real, chamada externa,
credencial, URL de sistema em uso, produto identificável ou dado de pessoa. Os
domínios de exemplo usam `.invalid`, que por definição não resolve.

---

## O que é um QA Agent Harness

Um agente de linguagem, solto, é um QA ruim. Ele conclui rápido demais, confunde
plausibilidade com evidência, inventa o comportamento esperado quando não o
encontra e às vezes age onde deveria apenas observar.

Um harness é o que corrige isso. Ele não deixa o agente mais inteligente — ele
torna o trabalho **verificável**. São cinco camadas:

| Camada | Pergunta que responde | Onde vive |
|---|---|---|
| **Agentes** | Quem faz o quê, e o que cada um **não** pode fazer | `.claude/agents/` |
| **Skills** | Como cada tipo de trabalho é executado, passo a passo | `.claude/skills/` |
| **Fluxos** | Qual a sequência, e quando parar | `harness/workflows/` |
| **Políticas** | O que é proibido, sempre | `harness/policies/` |
| **Contratos** | Qual o formato da saída, e quais campos são obrigatórios | `harness/contracts/` |

O efeito prático é simples: um agente que não consegue preencher os campos
obrigatórios de um contrato **não tem uma conclusão** — tem uma pendência. E
pendência vai para revisão humana em vez de virar afirmação.

---

## Como as partes se relacionam

```
                              pedido de QA
                                    │
                                    ▼
                            ┌───────────────┐
                            │   qa-leader   │  coordena; não executa,
                            └───────┬───────┘  não classifica, não abre bug
                                    │
             escolhe o fluxo em harness/workflows/
                                    │
        ┌───────────────┬───────────┼────────────┬──────────────────┐
        ▼               ▼           ▼            ▼                  ▼
 e2e-executor   api-test-analyst  ci-triage  scenario-reviewer  browser-explorer
   run-e2e        analyze-api   triage-ci-   generate-        browser-
                                 failure      scenarios       exploration
        │               │           │            │                  │
        │      cada um segue sua skill em .claude/skills/            │
        │      obedece a harness/policies/                          │
        │      raciocina com harness/prompts/                       │
        │      escreve na forma de harness/contracts/               │
        └───────────────┴───────────┼────────────┴──────────────────┘
                                    ▼
                     .qa-runs/  (execuções e evidências)
                                    │
                                    ▼
                          revisão e aprovação humana
                                    │
                                    ▼
                    app-memory/  (o que vale ser lembrado)
```

Duas direções de leitura:

- **De cima para baixo:** o pedido desce até um fluxo, que aciona um agente, que
  segue uma skill e produz uma saída no formato de um contrato.
- **De baixo para cima:** `app-memory/` alimenta a próxima execução com o que já
  se aprendeu — regras, riscos conhecidos, problemas recorrentes — para que o
  harness não recomece do zero a cada vez.

---

## As quatro capacidades, e por que não são a mesma coisa

Esta é a distinção que mais confunde quando se monta um harness de QA. As quatro
capacidades respondem a perguntas diferentes e **não se substituem**.

### Testes E2E/UI — `run-e2e`

**Pergunta:** os cenários que eu escrevi passam, neste ambiente, agora?

Roteiro fechado, resultado binário, repetível. O valor está em ser
**determinístico**: mesma entrada, mesmo resultado, sempre. É o que permite dizer
"isto regrediu" com confiança.

Limite: só encontra o que alguém pensou em cobrir.

### Análise de API — `analyze-api`

**Pergunta:** o contrato da camada de serviço é coerente, testável e coberto?

Trabalha sobre especificação e amostras sintéticas, sem executar nada. Enxerga o
que a interface esconde: código de resposta não documentado, campo obrigatório
ausente, formato de erro que muda entre operações, limite que ninguém definiu.

Limite: não observa o sistema em execução. Diz o que o contrato promete e onde a
promessa é ambígua — não o que o sistema faz.

### Triagem de CI — `triage-ci-failure`

**Pergunta:** em qual camada está esta falha, e com que confiança?

Não descobre falha — **classifica** falha que já aconteceu, em uma de cinco
camadas: `interface-e2e`, `api`, `automacao`, `ambiente` ou `configuracao`.

Essa classificação é o que decide quem trabalha em seguida. Chamar de defeito de
produto o que era configuração custa o dia de alguém; chamar de instabilidade o
que era defeito real custa a confiança na suíte inteira. Por isso a triagem exige
hipóteses concorrentes, confiança declarada e revisão humana obrigatória.

Limite: diagnostica; não corrige, não reexecuta, não abre ticket.

### Browser agent — `browser-exploration`

**Pergunta:** o que ninguém pensou em cobrir?

Exploração sem roteiro fechado, procurando estado quebrado, texto truncado,
elemento inacessível, comportamento estranho em caminho lateral. O produto é
**observação com evidência visual**.

Limite — e este é o ponto central:

> **O browser agent complementa a suíte E2E. Ele não a substitui.**
>
> Uma exploração não é repetível: o caminho percorrido depende do que o agente
> decidiu olhar. Ela não produz o "mesmo resultado sempre" que faz um teste de
> regressão valer. Ela não certifica que algo funciona — ela levanta suspeita com
> evidência.
>
> Um achado exploratório que valha guardar tem um destino: virar **cenário
> determinístico**, pelo fluxo `generate-scenarios`, e passar a ser executado por
> `run-e2e`. É assim que exploração se transforma em cobertura.
>
> Trocar uma suíte determinística por um agente explorando a interface troca
> regressão detectável por regressão provável. Nenhuma quantidade de sessões
> exploratórias fecha essa conta.

### Em resumo

| Capacidade | Encontra o desconhecido? | Repetível? | Certifica comportamento? |
|---|---|---|---|
| E2E/UI | não | sim | sim |
| Análise de API | parcialmente | sim | não (analisa contrato) |
| Triagem de CI | não (classifica) | sim | não |
| Browser agent | **sim** | **não** | **não** |

---

## Estrutura do diretório

```
Laboratorio/
├── .claude/          quem são os agentes e como cada trabalho é executado
│   ├── agents/       6 definições: papel, entradas, saídas, limites, escalonamento
│   └── skills/       4 procedimentos operacionais reutilizáveis
├── harness/          como o trabalho é conduzido
│   ├── workflows/    5 fluxos: entrada, etapas, saída, evidências, parada
│   ├── policies/     5 políticas: evidência, bug, aprovação, escrita, navegação
│   ├── contracts/    5 JSON Schemas (draft-07) das saídas estruturadas
│   └── prompts/      4 prompts que exigem JSON e incerteza declarada
├── app-memory/       o que o laboratório aprendeu (somente READMEs aqui)
├── .qa-runs/         execuções e evidências (única área de escrita livre)
└── .github/          3 workflows demonstrativos, apenas workflow_dispatch
```

Detalhe de cada diretório em `harness/README.md` e nos READMEs de
`app-memory/`.

---

## Invariantes do harness

Valem em todo fluxo, para todo agente, sem exceção:

1. **Escrita apenas em `.qa-runs/`.** Qualquer outro caminho exige aprovação
   humana que nomeie o caminho.
2. **Nenhum ticket externo** é criado, editado, comentado ou fechado pelo
   harness. Todo `bug-draft` é rascunho até uma pessoa aprovar.
3. **Nenhuma ação irreversível:** exclusão de dado, mudança administrativa,
   pagamento, envio de mensagem, exposição de segredo. Não há aprovação em tempo
   de execução que libere estas cinco.
4. **Nenhuma conclusão sem evidência.** Sem a evidência mínima definida em
   `evidence-policy.md`, a saída é `inconclusivo` ou hipótese declarada — nunca
   afirmação.
5. **Nenhum dado real.** Só dados fictícios e placeholders.
6. **Produção exige aprovação humana explícita**, com escopo e janela definidos.
7. **Incerteza é declarada, não escondida.** Confiança abaixo de `0.6` obriga
   apresentar as hipóteses concorrentes lado a lado, sem escolher.

---

## Sobre os workflows do GitHub

Os três arquivos em `.github/workflows/` são **demonstrativos**:

- acionados apenas por `workflow_dispatch`;
- sem segredo, token, URL real ou deploy;
- etapas de validação **simuladas** — nada é executado de verdade;
- geram um relatório fictício como artefato e encerram.

Como vivem em `Laboratorio/.github/workflows/`, e o GitHub só executa workflows
de `.github/workflows/` na raiz do repositório, eles são inertes. Estão aqui para
mostrar a forma, não para rodar.
