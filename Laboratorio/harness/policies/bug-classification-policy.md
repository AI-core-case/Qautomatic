# Política de classificação de bugs

Define quando um sintoma pode ser chamado de bug, como a severidade é sugerida e
o que nunca é bug.

Nenhum agente do harness abre ticket. Esta política governa **rascunhos**; a
decisão é humana — ver `human-approval-policy.md`.

## Antes de classificar: o sintoma é bug?

Percorra na ordem. A primeira resposta afirmativa determina o resultado.

1. **A automação está errada?** Espera fixa por tempo, seletor frágil,
   dependência de ordem, esperado desatualizado → classificação `automacao`,
   **não é bug de produto**.
2. **O ambiente falhou?** Dependência fora do ar, recurso esgotado, falha antes
   do primeiro passo do cenário → `ambiente`, **não é bug**.
3. **A configuração divergiu?** Variável ausente, versão diferente, permissão
   insuficiente, apontamento errado → `configuracao`, **não é bug**.
4. **O comportamento esperado está definido em algum lugar?** Se não está, é
   **lacuna de especificação** — pergunta para o time, não bug.
5. **A evidência mínima foi atingida?** Ver `evidence-policy.md`. Se não, é
   **achado com incerteza declarada**, não bug.

Sobreviveu aos cinco? Aí é candidato a `bug-draft`.

## Severidade sugerida

O agente **sugere**; a pessoa decide. Quatro níveis:

| Severidade | Critério objetivo |
|---|---|
| `critica` | Impede o uso do fluxo principal sem alternativa; indício de perda de dado; indício de exposição de dado ou de segredo |
| `alta` | Fluxo principal funciona apenas por caminho alternativo, ou fluxo secundário relevante está impedido; resultado incorreto apresentado como correto |
| `media` | Comportamento divergente com alternativa viável; erro de validação ou de mensagem que confunde sem impedir |
| `baixa` | Divergência cosmética, de texto ou de apresentação, sem impacto sobre o resultado |

Regras de arbitragem:

- Em dúvida entre dois níveis, sugira o **menor** e registre a dúvida. Inflar
  severidade destrói a utilidade da escala.
- Indício de perda ou exposição de dado é sempre `critica`, mesmo com evidência
  parcial — e vai para escalonamento imediato.
- Frequência não altera a severidade; entra como observação à parte.

## Campos obrigatórios do rascunho

Conforme `harness/contracts/bug-draft.schema.json`: título, descrição, passos,
resultado esperado, resultado atual, severidade sugerida e evidências.

Diretrizes:

- **Título**: o sintoma em uma linha, sem diagnóstico embutido.
- **Passos**: numerados, executáveis por outra pessoa, sem depender de estado
  não declarado.
- **Resultado esperado**: com a referência que o define.
- **Resultado atual**: o que se observou, não o que se conclui.
- **Evidências**: no mínimo duas, independentes, por caminho relativo.

## O que nunca é bug

- Comportamento que ninguém definiu — é lacuna de especificação.
- Falha de ambiente ou de configuração do pipeline.
- Cenário de teste errado, frágil ou desatualizado.
- Preferência de desenho sem requisito que a sustente.
- Sintoma observado uma vez, sem passos repetíveis.
- Impressão de exploração sem evidência visual.

## Reincidência

Antes de produzir rascunho, consulte `app-memory/known-issues/`. Sintoma já
registrado: aponte a reincidência e escale, em vez de duplicar.

## Proibições

Abrir, editar ou fechar ticket externo; ajustar severidade para acelerar
priorização; incluir dado pessoal ou segredo em qualquer campo; afirmar causa
raiz no rascunho quando só o sintoma está estabelecido.
