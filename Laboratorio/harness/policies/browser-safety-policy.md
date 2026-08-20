# Política de segurança de navegação

Governa toda interação do harness com interface, através da skill
`agent-browser` e do agente `browser-explorer`.

**Princípio:** o agente de navegação é um observador. Ele olha, registra e
captura. Ele não opera o sistema.

## Autorização de URL

- Só é navegável a URL **presente na lista de autorizadas do pedido**. Não existe
  URL autorizada por padrão.
- Link para fora da lista **não é seguido** — é registrado como "link não
  seguido", com o destino.
- Redirecionamento para fora da lista **encerra a sessão** e é registrado.
- Uma sessão sem lista de URLs autorizadas não começa.
- Sugira sempre domínios de exemplo não resolvíveis para laboratório (por
  exemplo, sob `.invalid`).

## Ambientes

| Ambiente | Condição |
|---|---|
| `laboratorio` | permitido, dentro dos limites desta política |
| `homologacao-exemplo` | permitido, dentro da janela informada no pedido |
| `producao` | **proibido** sem aprovação humana explícita registrada no pedido, com escopo e janela definidos |

Se o ambiente-alvo se revelar produção durante a sessão — ou houver
redirecionamento para produção — a sessão é **abortada imediatamente** e o fato é
escalado.

Em produção autorizada, o agente é **estritamente somente leitura**: navegação e
captura, nenhuma interação que altere estado.

## Antes de interagir: snapshot

Antes de **cada** interação — clique, preenchimento, envio, navegação — capture o
estado atual da tela.

Nenhuma interação acontece sem o estado anterior registrado. Este é o mecanismo
que permite dizer depois o que mudou e por causa de quê.

## Registro de passos

Todo passo entra no registro da sessão, na ordem, com o que foi feito e o que foi
observado. **Passo não registrado não aconteceu** — e, se não aconteceu no
registro, não sustenta achado nenhum.

O registro final vira `harness/contracts/browser-session.schema.json`.

## Captura em falhas

Todo comportamento inesperado, erro exibido ou estado quebrado gera **screenshot
imediato** em `.qa-runs/artifacts/browser/`, acompanhado do esperado e do
observado.

Observação sem evidência visual é registrada como **impressão**, não como
achado.

## Ações proibidas na interface

Nunca acione elemento cuja função seja:

- excluir, remover, limpar, redefinir ou apagar dado;
- alterar permissão, papel ou configuração administrativa;
- efetuar, confirmar ou cancelar pagamento;
- enviar mensagem, e-mail, convite ou notificação;
- aceitar termo, consentimento ou contrato em nome de alguém;
- disparar rotina administrativa, exportação em massa ou processamento em lote;
- confirmar qualquer operação anunciada como irreversível.

Diante de um desses elementos, o passo correto é **registrar que ele existe** —
não acioná-lo. Se ele é o único caminho para prosseguir, a sessão termina ali e
escala.

## Dados e credenciais

- Autenticação apenas com **usuário fictício de laboratório** fornecido no
  pedido. Nunca credencial real.
- Formulários recebem apenas placeholders explicitamente fictícios:
  `<nome-exemplo>`, `<identificador-generico>`, `<texto-de-teste>`.
- Nunca dado pessoal, documento, endereço, telefone ou meio de pagamento —
  inventado ou não.
- Tela exibindo token, chave, credencial ou algo com aparência de dado pessoal
  real: **o screenshot não é salvo**. Registre um alerta de segurança descrevendo
  onde está o problema, sem reproduzir o conteúdo.

## Limites de sessão

- Limite de passos e limite de duração são obrigatórios no pedido.
- Atingido qualquer um, a sessão encerra e registra o que ficou inexplorado.
- Interface em laço sem progresso encerra a sessão.

## Escalonamento imediato

Aborte e escale ao encontrar:

- indício de falha de segurança — registre o alerta e **não** aprofunde a
  exploração do problema;
- algo com cara de dado real de pessoa na tela;
- ação irreversível como único caminho possível;
- produção não autorizada, por acesso direto ou redirecionamento.

## Limite de escopo

Esta política cobre exploração **complementar**. O agente de navegação encontra
candidatos a cenário; ele não certifica que algo funciona. Confirmação de
comportamento é papel de cenário determinístico, executado pelo fluxo `run-e2e`.
