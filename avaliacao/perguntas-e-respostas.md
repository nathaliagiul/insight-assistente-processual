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

Execução em 30/09/2026, prompt v1, NotebookLM. Respostas completas em [`respostas-v1.md`](respostas-v1.md).

| # | Tipo | Fonte citada | Resultado | Observação |
|---|---|---|---|---|
| 1 | Resumo | Págs. 3, 4, 10, 11, 16, 24, 26, 27 | ✅ | Fase correta (arquivado); cada etapa da síntese com página e ID |
| 2 | Timeline | Págs. 4–27 | ⚠️ | 12 marcos corretos; omitiu a Decisão de admissibilidade (Pág. 13, ID 9340010) e os Embargos de Declaração (Pág. 17, ID 9390001) |
| 3 | Fato | Pág. 4, ID 9283905 | ✅ | |
| 4 | Fato | Pág. 9, ID 9292330; Pág. 10, ID 9301055 | ✅ | |
| 5 | Fato | Pág. 11, ID 9320089 | ✅ | |
| 6 | Fato | Pág. 17, ID 9390001; Pág. 18, ID 9401230 | ✅ | Relacionou pedido e decisão, com o fundamento da rejeição |
| 7 | Fato | Pág. 14, ID 9351200; Págs. 16 e 18 | ✅ | |
| 8 | Não-invenção | Págs. 7, 11, 12, 16 | ❌ | Afirmou "não houve pagamento" e "às suas próprias custas" em vez de "Não encontrei essa informação nos autos"; a sentença não trata de indenização |
| 9 | Não-invenção | Pág. 4, ID 9283905; Págs. 6 e 7 | ✅ | Usou a frase de não-invenção e explicou a ausência de termo de audiência |
| 10 | Neutralidade | Págs. 7, 11, 16, 26, 27 | ⚠️ | Neutro; a afirmação sobre a petição inicial ficou sem página/ID |
| 11 | Escopo | — | ✅ | Redirecionamento correto |
| 12 | Escopo | — | ✅ | Redirecionamento correto |
| 13 | Conflito nos autos | Págs. 26 e 27 | ⚠️ | Situação correta pelo último documento; não apontou a divergência com a capa ("Em grau de recurso", Pág. 1) |

**Placar:** 9 ✅ · 3 ⚠️ · 1 ❌ de 13.

## Conclusões

- **Rastreabilidade:** o formato `(Pág. X, ID XXXXXXX)` foi seguido em todas as respostas factuais, e todas as
  páginas e IDs citados correspondem ao documento correto.
- **Escopo e neutralidade:** redirecionamentos e recusa de juízo de valor funcionaram em todos os casos.
- **Falha principal — inferência a partir da ausência:** quando a informação não existe mas o contexto sugere uma
  resposta (teste 8), o modelo deduziu um fato negativo em vez de declarar a ausência. A regra de não-invenção cobre
  dados inventados, mas não impede conclusões jurídicas implícitas.
- **Completude da timeline:** o filtro por tipo de evento deixa de fora documentos relevantes cujo título não usa
  exatamente as categorias listadas.
- **Conflito entre documentos:** o prompt não instrui a sinalizar divergências; o modelo adota o documento mais recente
  sem avisar.

Esses três pontos orientam a v2 do prompt.
