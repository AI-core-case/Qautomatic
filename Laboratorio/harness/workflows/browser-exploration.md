# Fluxo: browser-exploration

Exploração de interface por agente, em ambiente autorizado.

- **Coordenador:** `qa-leader`
- **Executor:** `browser-explorer`
- **Skill:** `agent-browser`
- **Contrato de saída:** `harness/contracts/browser-session.schema.json`

## Objetivo

Procurar o que a suíte determinística não cobre: estado quebrado, texto
truncado, elemento inacessível, comportamento estranho em caminho lateral.

Este fluxo é **exploratório e complementar**. Ele encontra candidatos a cenário;
ele não certifica que algo funciona. Confirmação de comportamento é papel de
`run-e2e`.

## Entrada

| Campo | Obrigatório | Exemplo fictício |
|---|---|---|
| URLs autorizadas | sim | `https://ambiente-laboratorio.exemplo.invalid/inicio` |
| Ambiente | sim | `laboratorio`, `homologacao-exemplo`, `producao` |
| Objetivo em uma frase | sim | "observar o cadastro genérico à procura de estados inconsistentes" |
| Limite de passos | sim | `20` |
| Limite de duração | sim | `15 minutos` |
| Usuário fictício | quando houver autenticação | nunca credencial real |
| Aprovação humana | obrigatória se ambiente = `producao` | escopo e janela |

Falta qualquer campo obrigatório? A sessão não começa.

## Etapas

1. **Validar autorização** — URLs na lista, ambiente identificado, aprovação
   anexada se for produção. Ver `harness/policies/browser-safety-policy.md`.
2. **Abrir a sessão** — registrar URL inicial, ambiente, objetivo, limites, data
   e hora.
3. **Snapshot inicial** antes de qualquer interação.
4. **Laço de exploração**, dentro do limite de passos:
   - descrever o estado atual em uma frase;
   - escolher o próximo passo e confirmar que **não** é irreversível;
   - snapshot antes da interação;
   - executar, observar, comparar com a expectativa razoável;
   - divergiu → screenshot imediato mais o esperado e o observado;
   - registrar o passo.
5. **Encerrar** por objetivo atingido, limite alcançado ou critério de parada.
6. **Gravar** o `browser-session`.
7. **Encaminhar** cada observação que valha guardar para `generate-scenarios`,
   como candidata a cenário determinístico.

## Saída

- Um `browser-session` com URL, ambiente, passos executados, resultado,
  screenshots, data/hora e alertas de segurança.
- Lista de observações, cada uma com pelo menos um screenshot.
- Candidatas a cenário encaminhadas para `generate-scenarios`.

Observação sem evidência visual é registrada como **impressão**, não como
achado.

## Evidências

- Snapshot antes de cada interação.
- Screenshot imediato de todo comportamento inesperado.
- Registro ordenado de passos — passo não registrado não aconteceu.
- Tudo em `.qa-runs/artifacts/browser/`.

Tela com token, chave ou algo com aparência de dado pessoal real: **não salve o
screenshot**. Registre um alerta de segurança descrevendo onde está o problema,
sem reproduzir o conteúdo.

## Critérios de parada

- O único caminho para seguir exige ação irreversível.
- O ambiente se revela produção sem aprovação, ou há redirecionamento para
  produção.
- Aparece na tela algo com cara de dado real de pessoa.
- Aparece indício de falha de segurança — registre o alerta e **não** aprofunde
  a exploração do problema.
- Limite de passos ou de duração atingido.
- A interface entra em laço sem progresso.

## Proibições

Sair das URLs autorizadas; executar ação irreversível (excluir dado, alterar
permissão, efetuar pagamento, enviar mensagem, aceitar termo em nome de alguém,
disparar rotina administrativa); autenticar com credencial real; preencher dado
pessoal; capturar tela com segredo visível; abrir bug; tratar achado
exploratório como substituto de cenário determinístico.
