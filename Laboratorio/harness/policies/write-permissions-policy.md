# Política de permissões de escrita

Define onde o harness pode escrever e quais ações são bloqueadas em qualquer
circunstância.

## Escrita no repositório

**Permitido sem aprovação:**

- `.qa-runs/pending/` — execuções em andamento e pedidos de aprovação;
- `.qa-runs/completed/` — resultados de execuções concluídas;
- `.qa-runs/failed/` — resultados de execuções interrompidas;
- `.qa-runs/artifacts/` — evidências;
- `.qa-runs/artifacts/browser/` — evidências de navegação.

**Exige aprovação humana** (ver `human-approval-policy.md`):

- `app-memory/` — registrar conhecimento novo;
- `harness/` — políticas, contratos, fluxos, prompts;
- `.claude/` — definições de agentes e skills;
- `.github/` — automações;
- qualquer outro caminho do repositório.

**Somente leitura, sempre:** todo o restante.

## Ações bloqueadas

As cinco categorias abaixo são **bloqueadas**. Não há aprovação em tempo de
execução que as libere dentro deste harness. Diante de uma delas, o agente para,
registra o motivo e escala.

### 1. Exclusão de dado

Excluir, truncar, sobrescrever ou tornar irrecuperável qualquer registro, arquivo
ou conjunto de dados — em qualquer ambiente, inclusive laboratório. Sobrescrever
evidência já gravada também está incluído.

### 2. Mudanças administrativas

Alterar permissão, papel, grupo, política de acesso, configuração de segurança,
regra de rede ou parâmetro global. Criar, desativar ou elevar conta. Aceitar
termo ou consentimento em nome de alguém.

### 3. Pagamentos e operações financeiras

Iniciar, confirmar, alterar, estornar ou cancelar qualquer operação financeira,
cobrança, assinatura ou transferência. Inserir ou salvar meio de pagamento,
inclusive fictício.

### 4. Envio de mensagens

Enviar e-mail, mensagem, notificação, convite ou aviso a qualquer destinatário.
Publicar em canal externo. Acionar botão cuja função é notificar alguém. Vale
mesmo quando o destinatário aparente ser um endereço de teste.

### 5. Exposição de segredos

Ler, copiar, transcrever, registrar em log, capturar em tela ou incluir em
evidência qualquer credencial, token, chave, certificado ou segredo — ainda que
apareça na tela por acidente. Colocar segredo em arquivo, saída estruturada,
mensagem de commit ou artefato.

Ver também: gerar credencial nova, autenticar com credencial real e criar
variável de ambiente com valor sensível estão todos incluídos aqui.

## Rede

- O harness **não** faz chamada externa.
- `api-test-analyst` e `scenario-reviewer` operam sem rede.
- `browser-explorer` navega **exclusivamente** nas URLs autorizadas do pedido, em
  ambiente não produtivo (ou produtivo com aprovação registrada).
- URL fora da lista não é aberta — é registrada como link não seguido.

## Diante de um bloqueio

1. **Pare** antes da ação — não prepare, não deixe pronta para confirmar.
2. **Registre** em `.qa-runs/pending/`: a ação bloqueada, a categoria, o que a
   tornou necessária e o efeito esperado.
3. **Escale** ao `qa-leader` com a pergunta em uma frase.
4. **Prossiga** com o restante do trabalho que não depende daquela ação, e diga
   ao final o que ficou de fora e por quê.

## Proibições explícitas

- Contornar um bloqueio por caminho indireto (comando alternativo, ferramenta
  diferente, passo fatiado).
- Executar ação bloqueada "só em laboratório" — a lista não distingue ambiente.
- Escrever fora de `.qa-runs/` sem aprovação nomeando o caminho.
- Interpretar uma aprovação de escrita como aprovação para uma ação bloqueada.
