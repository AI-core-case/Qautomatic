---
name: agent-browser
description: Procedimento seguro para exploração de interface por agente em ambiente autorizado — URLs permitidas, snapshot antes de interagir, registro de passos, screenshot em falhas, nenhuma ação irreversível e produção somente com aprovação explícita.
---

# Skill: agent-browser

Procedimento de navegação assistida para exploração de interface. É a única
forma autorizada de o harness interagir com uma interface fora de um cenário
determinístico.

Esta skill é **observacional**. Ela existe para ver, registrar e capturar — não
para operar o sistema.

## Regras invioláveis

Estas seis regras valem em toda sessão, sem exceção:

1. **Somente URLs autorizadas.** Navegue apenas para URLs presentes na lista de
   autorizadas do pedido. URL fora da lista não é aberta — é registrada como
   "link não seguido". Redirecionamento para fora da lista encerra a sessão.
2. **Snapshot antes de interagir.** Antes de **cada** interação (clique,
   preenchimento, envio), capture o estado atual da tela. Nenhuma interação
   acontece sem o estado anterior registrado.
3. **Registrar todos os passos.** Cada passo entra no registro da sessão, na
   ordem, com o que foi feito e o que foi observado. Passo não registrado não
   aconteceu.
4. **Screenshot em falhas.** Todo comportamento inesperado, erro exibido ou
   estado quebrado gera captura de tela imediata em
   `.qa-runs/artifacts/browser/`.
5. **Nenhuma ação irreversível.** Nunca: excluir dado, alterar permissão,
   efetuar pagamento, enviar mensagem, aceitar termo em nome de alguém, disparar
   rotina administrativa, confirmar operação sem volta. Diante de um botão
   assim, o passo é *registrar que ele existe* — não acioná-lo.
6. **Produção somente com aprovação explícita.** Ambiente de produção exige
   aprovação humana registrada no pedido, com escopo e janela. Sem isso, a
   sessão não começa. Ver `harness/policies/browser-safety-policy.md`.

## Pré-condições

1. Lista de URLs autorizadas presente no pedido.
2. Ambiente identificado (`laboratorio`, `homologacao-exemplo`, `producao`).
3. Objetivo da exploração em uma frase.
4. Limite de passos e de duração definidos.
5. Usuário fictício de laboratório, quando houver autenticação. **Nunca**
   credencial real.
6. `.qa-runs/artifacts/browser/` disponível para as evidências.

Falta alguma? A sessão não começa. Pergunte.

## Procedimento

1. **Abrir a sessão**: registre URL inicial, ambiente, objetivo, limites, data e
   hora.
2. **Snapshot inicial** da primeira tela, antes de qualquer interação.
3. **Laço de exploração**, respeitando o limite de passos:
   1. descreva o estado atual em uma frase;
   2. escolha o próximo passo e verifique que ele **não** é irreversível;
   3. capture o snapshot anterior à interação;
   4. execute o passo;
   5. observe o resultado e compare com a expectativa razoável;
   6. divergiu? screenshot imediato mais descrição do que se esperava e do que
      apareceu;
   7. registre o passo.
4. **Encerrar** ao atingir o objetivo, o limite de passos, o limite de duração ou
   um critério de parada.
5. **Gravar** a sessão conforme
   `harness/contracts/browser-session.schema.json`.
6. **Encaminhar** cada observação que valha guardar como sugestão de cenário
   determinístico para o `scenario-reviewer`.

## Dados em formulário

Somente placeholders explicitamente fictícios: `<nome-exemplo>`,
`<identificador-generico>`, `<texto-de-teste>`. Nunca dado pessoal, documento,
meio de pagamento ou credencial — inventado ou não.

## Captura de tela e segredos

Se a tela exibe token, chave, credencial ou algo com aparência de dado pessoal
real: **não salve o screenshot**. Registre um alerta de segurança descrevendo
onde o problema está, sem reproduzir o conteúdo.

## Critérios de parada

Encerre a sessão imediatamente quando:

- o único caminho para seguir exigir uma ação irreversível;
- o ambiente se revelar produção sem aprovação, ou houver redirecionamento para
  produção;
- aparecer na tela algo com cara de dado real de pessoa;
- aparecer indício de falha de segurança — registre o alerta e **não** explore o
  problema mais a fundo;
- o limite de passos ou de duração for atingido;
- a interface entrar em laço sem progresso.

## Limite de escopo

Esta skill **complementa** a suíte E2E. Ela encontra o que ninguém pensou em
cobrir; ela não confirma que algo funciona. Confirmação de comportamento é papel
de cenário determinístico executado por `execute-e2e`.
