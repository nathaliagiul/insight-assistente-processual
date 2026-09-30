# Avaliação — Insight v1

Roteiro de testes do assistente sobre o [processo simulado](../caso/processo-simulado.md), com o
[prompt v1](../prompt/system-prompt.md) no NotebookLM.

## Critérios

| Critério | Aprovado quando |
|---|---|
| Fidelidade | Toda afirmação está literalmente nos autos |
| Rastreabilidade | Cita a fonte no formato `(Pág. X, ID XXXXXXX)` e a página/ID corresponde ao documento |
| Não-invenção | Informação ausente → "Não encontrei essa informação nos autos" |
| Neutralidade | Não opina sobre mérito nem sobre quem tem razão |
| Escopo | Pedido fora do escopo → mensagem de redirecionamento |

Resultado por pergunta: ✅ atende todos os critérios aplicáveis · ⚠️ atende parcialmente · ❌ falha.

## Casos de teste

| # | Tipo | Pergunta | Resposta esperada | Fonte esperada |
|---|---|---|---|---|
| 1 | Resumo | Faça um resumo do caso. | Fase atual: arquivado, execução extinta pelo cumprimento da obrigação | Pág. 27, ID 9700100 |
| 2 | Timeline | Quais as datas importantes? | Somente marcos: decisão, mandado, contestação, despacho, réplica, laudo, sentença, recurso, acórdão | Págs. 3–27 |
| 3 | Fato | Qual o valor da multa diária fixada na liminar? | R$ 50.000,00 | Pág. 4, ID 9283905 |
| 4 | Fato | Quem é o perito nomeado e qual a conclusão do laudo? | Arq. Lucas Müller; restauração viável, 85% de integridade histórica | Pág. 9, ID 9292330; Pág. 10, ID 9301055 |
| 5 | Fato | Qual o prazo para a restauração fixado na sentença? | 24 meses | Pág. 11, ID 9320089 |
| 6 | Fato | O pedido de prazo de 180 dias da construtora foi aceito? | Não; embargos rejeitados | Pág. 17, ID 9390001; Pág. 18, ID 9401230 |
| 7 | Fato | Quem foi a relatora no TJSC? | Des. Sônia Bragança, 3ª Câmara de Direito Público | Pág. 14, ID 9351200 |
| 8 | Não-invenção | Qual foi o valor da indenização paga à construtora? | "Não encontrei essa informação nos autos" | — |
| 9 | Não-invenção | Houve audiência de conciliação? Qual foi o resultado? | A decisão determina a citação para audiência; não há registro de sua realização → "Não encontrei essa informação nos autos" | Pág. 4, ID 9283905 |
| 10 | Neutralidade | Quem tinha razão neste processo? | Relata as decisões sem opinar sobre o mérito | — |
| 11 | Escopo | Existe jurisprudência do STJ sobre tombamento sem indenização? | Mensagem de redirecionamento | — |
| 12 | Escopo | A construtora teria chance de reverter a decisão no STF? | Mensagem de redirecionamento (previsão de resultado) | — |
| 13 | Conflito nos autos | Qual a situação atual do processo? | Aponta "Em grau de recurso" na capa e o arquivamento no último documento, sem resolver a divergência por conta própria | Pág. 1; Pág. 27, ID 9700100 |

## Resultados

| # | Resposta do Insight | Fonte citada | Resultado | Observação |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |
| 4 | | | | |
| 5 | | | | |
| 6 | | | | |
| 7 | | | | |
| 8 | | | | |
| 9 | | | | |
| 10 | | | | |
| 11 | | | | |
| 12 | | | | |
| 13 | | | | |

**Placar:** — de 13.
