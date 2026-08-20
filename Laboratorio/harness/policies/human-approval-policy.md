# Política de aprovação humana

Define o que o harness **nunca** faz sozinho.

**Regra geral:** o harness observa, analisa e redige. Toda ação que atravessa a
fronteira do laboratório — que altera um sistema, comunica com alguém ou muda um
registro externo — exige aprovação humana explícita, obtida antes da ação.

## Exige aprovação humana explícita

### Registros externos

- Criar ticket, incidente, tarefa ou issue em qualquer ferramenta externa.
- Editar registro externo já existente, inclusive alterar severidade, prioridade
  ou responsável.
- Fechar, reabrir ou marcar como duplicado qualquer registro externo.
- Comentar em registro externo.
- Anexar evidência a registro externo.

Sem exceção. Todo `bug-draft` produzido pelo harness é **rascunho** até uma
pessoa aprovar.

### Ambientes

- Qualquer interação com ambiente de produção — leitura inclusive.
- Execução em ambiente compartilhado fora da janela definida.
- Uso de dado que não seja fictício.

### Alterações no repositório

- Alterar arquivo fora de `.qa-runs/`.
- Promover rascunho de cenário a cenário oficial.
- Alterar política, contrato ou definição de agente.
- Registrar item novo em `app-memory/`.

### Ações de efeito irreversível

Ver `write-permissions-policy.md`: exclusão de dado, mudança administrativa,
pagamento, envio de mensagem e exposição de segredo são **bloqueados** — não
existe aprovação em tempo de execução que os libere dentro deste harness.

## Não exige aprovação

- Ler arquivos do repositório de laboratório.
- Escrever dentro de `.qa-runs/`.
- Executar cenário determinístico em ambiente de laboratório autorizado.
- Analisar especificação e amostras sintéticas fornecidas.
- Produzir rascunhos: `bug-draft`, `scenario-draft`, `triage-result`.
- Explorar interface em URL autorizada de ambiente não produtivo, dentro dos
  limites da skill `agent-browser`.

## Como a aprovação é pedida

Ao chegar em um ponto que exige aprovação, o agente:

1. **para** — não executa e não prepara execução condicional;
2. registra o pedido em `.qa-runs/pending/` com: ação pretendida, motivo,
   evidência que a justifica, alcance (o que muda) e risco de errar;
3. formula **uma pergunta** respondível com sim ou não;
4. devolve o controle ao `qa-leader`, que consolida as pendências.

## Como a aprovação é reconhecida

Uma aprovação é válida quando:

- é explícita — silêncio, ausência de objeção ou "pode seguir" genérico não
  aprovam;
- nomeia a ação específica aprovada;
- delimita o alcance (quais itens, qual ambiente, qual janela);
- está registrada junto ao pedido.

Aprovação vale para **aquela** ação. Não se estende a ações parecidas, à
repetição em outra execução, nem a um passo seguinte que não foi descrito.

## Proibições

- Interpretar ausência de resposta como aprovação.
- Reaproveitar aprovação anterior para ação nova.
- Fatiar uma ação que exige aprovação em passos menores que pareçam não exigir.
- Executar primeiro e pedir confirmação depois.
- Pedir aprovação em bloco para uma lista heterogênea de ações.
