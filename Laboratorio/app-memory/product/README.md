# app-memory/product/

Memória de **produto**: o que o sistema sob teste faz, em termos que sustentam
uma decisão de QA.

Existe para responder à pergunta que trava toda análise: *qual era o
comportamento esperado?* Sem isso, um agente só consegue dizer que algo é
diferente — nunca que está errado.

## O que pode ser registrado aqui

- Descrição funcional dos fluxos, em linguagem de comportamento observável.
- Regras de negócio genéricas relevantes para teste (o que é obrigatório, o que é
  opcional, o que é proibido).
- Estados possíveis de uma entidade e as transições válidas entre eles.
- Vocabulário do domínio, para que cenários e bugs usem os mesmos termos.
- Decisões de produto que explicam por que um comportamento aparentemente
  estranho é intencional.

## O que não pode ser registrado aqui

- Dado de pessoa: nome, documento, e-mail, telefone, endereço.
- Dado de cliente ou de contrato.
- Credencial, token, chave, certificado ou qualquer segredo.
- Endpoint interno, hostname privado, string de conexão.
- Cópia de dado de produção, ainda que parcial ou anonimizada por aproximação.
- Documento interno confidencial.

Use sempre nomes fictícios e placeholders (`<usuario-exemplo>`,
`<identificador-generico>`).

## Como registrar

Um arquivo por tema, título no infinitivo ou no substantivo do domínio. Cada
comportamento registrado diz **onde está definido** — sem essa referência, o
material vira opinião e não sustenta classificação de bug.

## Quem escreve

Escrita aqui **exige aprovação humana** — ver
`harness/policies/human-approval-policy.md`. Agentes leem; pessoas registram.
