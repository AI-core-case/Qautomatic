# Fluxo: generate-scenarios

Criação e revisão de cenários de teste genéricos.

- **Coordenador:** `qa-leader`
- **Executor:** `scenario-reviewer`
- **Contrato de saída:** `harness/contracts/scenario-draft.schema.json`

## Objetivo

Produzir cenários determinísticos, legíveis e não redundantes a partir de uma
descrição de comportamento — e revisar os cenários que já existem quanto a
clareza, cobertura, determinismo e risco.

Todo cenário sai deste fluxo como **rascunho**.

## Entrada

| Campo | Obrigatório | Observação |
|---|---|---|
| Descrição do comportamento a cobrir | sim (modo criação) | fictícia |
| Cenários existentes | sim (modo revisão) | — |
| Regras de escrita | não | `app-memory/qa-rules/` |
| Riscos e lacunas conhecidos | não | `app-memory/test-knowledge/` |
| Prioridade pretendida | não | `baixa`, `media`, `alta`, `critica` |

## Etapas

### Modo criação

1. **Reformular o comportamento** em uma frase verificável. Ambíguo? Pergunte
   antes de escrever.
2. **Levantar as variações**: caminho principal, erro de entrada, falta de
   permissão, limite, estado inicial ausente.
3. **Escrever cada cenário** com título, pré-condições, passos numerados,
   resultado esperado, prioridade e riscos.
4. **Verificar determinismo** — reprovar qualquer cenário cujo resultado dependa
   de hora, ordem de execução ou dado preexistente.
5. **Verificar irreversibilidade** — passo que exclui dado, altera permissão,
   efetua pagamento ou envia mensagem obriga
   `requer_aprovacao_humana: true`.
6. **Consolidar redundância** — cenários que testam a mesma coisa viram um.
7. **Gravar** os `scenario-draft`.

### Modo revisão

1. **Ler o cenário como quem chega depois**: dá para executar sem perguntar nada?
2. **Avaliar** clareza, determinismo, cobertura real, risco e redundância.
3. **Emitir veredito** por cenário: `aprovar-com-ressalva`, `ajustar` ou
   `descartar`, com justificativa em uma frase.
4. **Quando é `ajustar`**, entregar o texto proposto junto.

## Saída

- `scenario-draft` em rascunho, um por cenário.
- Parecer de revisão com veredito e justificativa.
- Lista de perguntas abertas, quando a descrição era ambígua.

## Evidências

Este fluxo não produz evidência de execução — produz rastreabilidade:

- cada cenário aponta qual comportamento descrito ele cobre;
- cada veredito de revisão aponta o trecho do cenário que o motivou;
- cada risco listado diz o que aconteceria se não fosse coberto.

## Critérios de parada

- Comportamento esperado ambíguo → pergunta específica, sem escolher
  interpretação.
- Requisito parece ter mudado desde o cenário existente → escala, não reescreve
  em silêncio.
- Risco identificado é de segurança, privacidade ou perda de dado → escala antes
  de detalhar os passos.
- Conflito com `app-memory/qa-rules/` → escala.

## Proibições

Executar o cenário criado; escrever cenário não determinístico; usar dado real
(nome, documento, endereço, valor contratual, credencial) — apenas placeholders;
inflar cobertura com redundância; promover rascunho a cenário oficial sem
aprovação humana.
