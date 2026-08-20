# Política de evidências

Define o que conta como evidência no harness e qual é o mínimo para cada tipo de
conclusão. Vale para todos os agentes e fluxos.

**Princípio:** uma conclusão de QA vale o que vale a evidência que a sustenta.
Sem evidência, existe hipótese — e hipótese é rotulada como tal.

## O que conta como evidência

1. **Captura de tela** com data/hora, armazenada em `.qa-runs/artifacts/`.
2. **Trecho identificado de registro de execução**, com a referência que permite
   localizá-lo.
3. **Resultado estruturado** já gravado conforme um contrato de
   `harness/contracts/`.
4. **Referência a especificação** — o ponto exato do documento fictício que
   define o comportamento esperado.
5. **Amostra sintética de requisição e resposta**, quando a análise é de API.
6. **Observação descrita e acompanhada de captura**, no caso de exploração de
   interface.

## O que não conta como evidência

- Descrição de memória, sem artefato ("eu vi que a tela quebrou").
- Plausibilidade ("esse tipo de erro costuma ser de ambiente").
- Reincidência sozinha ("já aconteceu antes, então é a mesma causa").
- Saída de agente sem referência a artefato.
- Captura que não foi salva.
- Consenso entre agentes — dois agentes concordarem não produz evidência.

## Evidência mínima por conclusão

| Conclusão | Evidência mínima |
|---|---|
| `test-result` com `status: passou` | registro da execução do cenário, com o estado final observado |
| `test-result` com `status: falhou` | captura do estado no momento da falha (ou motivo registrado da falha de captura) **e** trecho do registro **e** esperado/observado em uma frase |
| `test-result` com `status: instavel` | dois registros de execução do **mesmo** cenário, no **mesmo** estado, com resultados diferentes |
| `test-result` com `status: bloqueado` | registro do que impediu a execução |
| `triage-result` com classificação definida | pelo menos uma evidência verificável, mais as hipóteses concorrentes consideradas |
| `triage-result` com `classificacao: inconclusivo` | lista do que falta para decidir |
| `bug-draft` | passos que levam ao sintoma **e** duas evidências independentes **e** o esperado com sua referência |
| `browser-session` com achado | pelo menos um screenshot por achado |
| Achado de API como defeito | referência na especificação **e** referência na amostra |

## Regra sobre falha classificada como bug

Um `bug-draft` só pode ser produzido quando, cumulativamente:

1. os passos que levam ao sintoma estão escritos e são repetíveis por uma pessoa;
2. existem **duas** evidências independentes do sintoma (por exemplo: captura de
   tela mais trecho do registro de execução);
3. o resultado esperado está declarado **com a referência** que o define — não
   com a opinião do agente;
4. a hipótese de erro na automação foi considerada e registrada como afastada,
   com o motivo;
5. nenhuma evidência contém segredo ou dado pessoal.

Falta um item? A saída é achado com incerteza declarada, não `bug-draft`.

## Armazenamento

- Evidências de execução e triagem: `.qa-runs/artifacts/`.
- Evidências de navegação: `.qa-runs/artifacts/browser/`.
- Referência sempre por **caminho relativo** dentro do documento estruturado.
- Execução em andamento fica em `.qa-runs/pending/`; ao terminar vai para
  `completed/` ou `failed/`.

## Sanitização

Antes de qualquer evidência ser gravada:

- mascare o que tiver aparência de credencial, token ou chave;
- não grave dado pessoal, ainda que fictício por acidente;
- tela com segredo visível **não** é capturada — registre um alerta de segurança
  descrevendo o problema sem reproduzir o conteúdo.

Evidência que não pode ser sanitizada não é gravada. O achado é registrado sem
ela, com o motivo.

## Retenção

Guarde o mínimo necessário para sustentar a conclusão. Captura duplicada do mesmo
estado é descartada. Nenhuma evidência é mantida "por precaução" quando não
sustenta nada.
