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

O prompt do sistema está na versão [v3](prompt/system-prompt-v3.md), resultado de duas rodadas de avaliação
([v1](prompt/system-prompt.md) → [v2](prompt/system-prompt-v2.md) → v3). É organizado em sete blocos:

| Bloco | Técnica |
|---|---|
| Persona | Identidade fixa, tom formal |
| Missão e escopo | Separação entre fonte de regras (manual do produto) e fonte de fatos (autos) |
| Raciocínio | Chain of Thought em 5 passos: intenção → busca → verificação → resposta → neutralidade |
| Restrições | Não-invenção, não-inferência (ausência de registro não é fato), divergência entre documentos, fonte correta de citação, redirecionamento fora do escopo |
| Exemplos | Few-shot para resumo, timeline, informação inexistente, divergência, neutralidade e pergunta fora do escopo |
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
├── prompt/system-prompt-v2.md        # revisão após a avaliação da v1
├── prompt/system-prompt-v3.md        # revisão após a avaliação da v2 (atual)
├── caso/processo-simulado.md         # autos fictícios de uma Ação Civil Pública (27 páginas)
├── avaliacao/perguntas-e-respostas.md  # casos de teste, resultados, comparação e pontos em aberto
├── avaliacao/respostas-v1.md           # respostas com o prompt v1, sem edição
├── avaliacao/respostas-v2.md           # respostas com o prompt v2, sem edição
└── avaliacao/respostas-v3.md           # respostas com o prompt v3, sem edição
```

## Estudo de caso

Os testes usam uma **Ação Civil Pública fictícia** de tombamento de um imóvel histórico, com 25 documentos da petição
inicial ao arquivamento: liminar, contestação, perícia, sentença, apelação, acórdão, embargos, trânsito em julgado e
cumprimento de sentença. O processo percorre primeiro e segundo grau e contém uma divergência proposital entre a capa
("em grau de recurso") e o último andamento (arquivamento).

## Avaliação

O [roteiro de avaliação](avaliacao/perguntas-e-respostas.md) tem 13 casos de teste cobrindo fidelidade,
rastreabilidade, não-invenção, neutralidade, escopo e conflito de informação nos autos.

| Resultado | v1 | v2 | v3 |
|---|---|---|---|
| ✅ Atende | 9 | 11 | 12 |
| ⚠️ Parcial | 3 | 2 | 1 |
| ❌ Falha | 1 | 0 | 0 |

Em todas as versões, as citações de página e ID dos autos apontaram o documento correto e os testes de escopo
passaram.

| Versão | Mudança | Efeito |
|---|---|---|
| v1 → v2 | Princípio de que ausência de registro não é fato; timeline classificada pelo conteúdo do documento; citação em cada frase | Eliminou a única falha (o modelo concluía "não houve pagamento" sobre uma indenização inexistente) e as omissões da timeline |
| v2 → v3 | Capa do processo tratada como documento, com comparação obrigatória da situação; formato próprio para citar o manual | Passou a sinalizar a divergência entre capa e último andamento e parou de inventar IDs |

Em aberto na v3: no teste de neutralidade, o modelo copiou literalmente o início do exemplo few-shot. Detalhes em
[pontos em aberto](avaliacao/perguntas-e-respostas.md#pontos-em-aberto).

## Como reproduzir

1. Exporte [`caso/processo-simulado.md`](caso/processo-simulado.md) para PDF como `2_Processo.pdf`, com uma página por
   documento e o número da página impresso no rodapé (`Página X`).
2. Exporte [`docs/visao-produto.md`](docs/visao-produto.md) para PDF como `1_Insight.pdf`.
3. Crie um notebook no [NotebookLM](https://notebooklm.google.com) e carregue os dois PDFs como fontes.
4. Nas configurações da conversa do notebook, use o modo personalizado e cole o conteúdo do bloco de texto de
   [`prompt/system-prompt-v3.md`](prompt/system-prompt-v3.md).
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
