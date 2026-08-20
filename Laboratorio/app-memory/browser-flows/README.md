# app-memory/browser-flows/

Memória de **fluxos de interface mapeados**: os caminhos que uma sessão de
exploração já percorreu e o que se sabe sobre eles.

Existe para que o agente `browser-explorer` não recomece do zero a cada sessão —
e, principalmente, para que ele saiba de antemão onde **não** deve clicar.

## O que pode ser registrado aqui

- Caminhos de navegação mapeados, em termos de telas e transições.
- Pontos de entrada de cada fluxo em ambiente de laboratório.
- **Ações irreversíveis já identificadas na interface**, com a tela em que
  aparecem — o registro mais valioso deste diretório.
- Elementos e regiões que exigem cuidado: telas que exibem dado sensível, áreas
  administrativas, confirmações sem volta.
- Estados difíceis de alcançar e como chegar até eles usando dados fictícios.
- Heurísticas de exploração que já renderam achado neste fluxo.
- Limites de sessão recomendados por fluxo.

## O que não pode ser registrado aqui

- URL de ambiente real. Use apenas exemplos não resolvíveis, por exemplo sob
  `.invalid`.
- Credencial, ainda que de usuário de teste.
- Dado de pessoa exibido em tela.
- Captura de tela com segredo ou dado sensível.
- Passo a passo para acionar uma ação irreversível — registre **que ela existe e
  onde**, nunca como executá-la.

## Como registrar

Um arquivo por fluxo. Comece pelo ponto de entrada, siga pelas transições, e
marque de forma inequívoca cada ação irreversível encontrada.

Um fluxo mapeado aqui não é um cenário de teste. Fluxo mapeado descreve o
território; cenário determinístico verifica comportamento e vive em `harness/`
mais a suíte oficial. Achado exploratório que valha guardar deve virar
`scenario-draft` pelo fluxo `generate-scenarios`.

## Quem escreve

Escrita aqui **exige aprovação humana** — ver
`harness/policies/human-approval-policy.md`. O agente propõe a partir de um
`browser-session`; a pessoa confirma o que entra na memória.
