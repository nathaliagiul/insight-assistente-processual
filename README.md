# Insight — Assistente de Inteligência Processual

Assistente de IA que lê autos de processos judiciais e entrega resumo, linha do tempo de marcos processuais e respostas
a perguntas, **sempre citando a página e o ID do documento de origem** e recusando-se a responder o que não está nos
autos.

Projeto desenvolvido na pós-graduação em IA e Automação da Rocketseat. Implementado no **NotebookLM** com engenharia de
prompt sobre contexto longo, sem código de aplicação.

## Problema

Representantes processuais (advogados, procuradores, defensores) acompanham dezenas de processos com centenas de
páginas cada. Retomar o contexto de um processo para responder a um cliente ou preparar uma audiência exige releitura
dos autos. Ferramentas de IA genéricas aceleram a leitura, mas inventam fatos e não indicam de onde tiraram a
informação, o que as torna inutilizáveis em contexto jurídico.

## Solução

| Função | Entrega |
|---|---|
| Resumo executivo | Fase processual, último andamento e síntese de 5 a 15 linhas |
| Timeline | Apenas marcos processuais (decisão, contestação, laudo, sentença, recurso, acórdão…), ignorando movimentações de sistema |
| Chat Q&A | Respostas livres sobre os autos para preparação de audiências |

Regras que valem para todas as funções:

- **Não-invenção:** informação ausente dos autos → "Não encontrei essa informação nos autos".
- **Rastreabilidade:** toda afirmação cita `(Pág. X, ID XXXXXXX)`.
- **Neutralidade:** relata os fatos narrados pelas partes, sem opinar sobre o mérito.
- **Escopo fechado:** sem jurisprudência externa e sem previsão de resultado.

## Arquitetura do prompt

O [prompt do sistema](prompt/system-prompt.md) é organizado em sete blocos:

| Bloco | Técnica |
|---|---|
| Persona | Identidade fixa, tom formal |
| Missão e escopo | Separação entre fonte de regras (manual do produto) e fonte de fatos (autos) |
| Raciocínio | Chain of Thought em 5 passos: intenção → busca → verificação → resposta → neutralidade |
| Restrições | Não-invenção, proibição de inferência jurídica, redirecionamento fora do escopo |
| Exemplos | Few-shot para resumo, timeline e pergunta fora do escopo |
| Saída | Contrato de formato: títulos e padrão de citação |
| Rastreabilidade | Ancoragem na paginação impressa no rodapé do PDF, não na paginação interna da ferramenta |

O último bloco trata a divergência entre a contagem de páginas da ferramenta e a numeração dos autos: o prompt
instrui o modelo a usar o número impresso no rodapé e fornece âncoras textuais (título da página → número esperado)
para calibrar.

## Estrutura do repositório

```
insight-assistente-processual/
├── docs/visao-produto.md             # visão, persona, jornadas, funcionalidades, roadmap, FAQ
├── prompt/system-prompt.md           # configuração do assistente (v1)
├── caso/processo-simulado.md         # autos fictícios de uma Ação Civil Pública (27 páginas)
├── avaliacao/perguntas-e-respostas.md  # casos de teste, resultados e conclusões
└── avaliacao/respostas-v1.md           # respostas do assistente, sem edição
```

## Estudo de caso

Os testes usam uma **Ação Civil Pública fictícia** de tombamento de um imóvel histórico, com 25 documentos da petição
inicial ao arquivamento: liminar, contestação, perícia, sentença, apelação, acórdão, embargos, trânsito em julgado e
cumprimento de sentença. O processo percorre primeiro e segundo grau e contém uma divergência proposital entre a capa
("em grau de recurso") e o último andamento (arquivamento).

## Avaliação

O [roteiro de avaliação](avaliacao/perguntas-e-respostas.md) tem 13 casos de teste cobrindo fidelidade,
rastreabilidade, não-invenção, neutralidade, escopo e conflito de informação nos autos.

| Resultado v1 | Casos |
|---|---|
| ✅ Atende | 9 |
| ⚠️ Parcial | 3 |
| ❌ Falha | 1 |

Todas as citações de página e ID apontaram o documento correto, e os testes de escopo e neutralidade passaram. A falha
ocorreu em uma pergunta sobre informação inexistente (valor de indenização): o modelo concluiu que "não houve
pagamento" em vez de declarar que a informação não consta dos autos. Os pontos parciais foram omissões na timeline,
uma afirmação sem citação e a ausência de alerta sobre uma divergência entre documentos.

## Como reproduzir

1. Exporte [`caso/processo-simulado.md`](caso/processo-simulado.md) para PDF como `2_Processo.pdf`, com uma página por
   documento e o número da página impresso no rodapé (`Página X`).
2. Exporte [`docs/visao-produto.md`](docs/visao-produto.md) para PDF como `1_Insight.pdf`.
3. Crie um notebook no [NotebookLM](https://notebooklm.google.com) e carregue os dois PDFs como fontes.
4. Nas configurações da conversa do notebook, use o modo personalizado e cole o conteúdo do bloco de texto de
   [`prompt/system-prompt.md`](prompt/system-prompt.md).
5. Execute as perguntas de [`avaliacao/perguntas-e-respostas.md`](avaliacao/perguntas-e-respostas.md).

## Limitações

- Validado com um único processo fictício de 27 páginas; não testado com autos reais de centenas de páginas.
- Depende do NotebookLM: sem API, sem integração com sistemas judiciais (PJe) e sem controle sobre o modelo.
- A citação de página depende de o PDF ter numeração impressa no rodapé.

## Roadmap

| Fase | Entrega |
|---|---|
| 1 — MVP | Resumo do status atual com citação de fonte |
| 2 | Chat livre para preparação de audiências |
| 3 | Análise de jurisprudência e comparação com casos similares |

---

> O processo, as partes, as pessoas e a empresa usados neste repositório são fictícios.
