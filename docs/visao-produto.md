# Insight — Documento do Produto

| Campo | Valor |
|---|---|
| Produto | Insight — Módulo de IA para Análise Processual |
| Versão do documento | 1.0 |
| Status | Definição de escopo |
| Atualização | 09/12/2025 |
| Responsável | Nathalia Pereira Lopes |

## 1. Visão do produto

O Insight é um assistente de inteligência de documentos integrado ao fluxo jurídico. Lê, compreende e sintetiza a
totalidade dos autos processuais, combatendo a "infoxicação documental".

Diferente da busca por palavras-chave, atua como um auxiliar que lê o processo e responde a perguntas indicando o
documento de origem, oferecendo segurança, velocidade de resposta e redução de horas de leitura passiva.

## 2. Discovery

### Persona principal

Representante processual (advogado, procurador ou defensor). Trabalha sob pressão constante de prazos e gerencia
múltiplos processos simultaneamente. Valoriza precisão técnica e precisa de agilidade.

### Dores

- **Tempo:** releitura de autos antigos para retomar o contexto.
- **Ansiedade:** pressão para responder clientes imediatamente sem ter os dados em mãos.
- **Risco de erro:** perder um detalhe ou prazo em processos com centenas de páginas.

### Jornadas do usuário

**1. Cliente solicita atualização processual**
1. O advogado baixa os autos digitais do sistema judicial.
2. Carrega o documento com o andamento atualizado no Insight.
3. O Insight gera o resumo processual e fica disponível para perguntas sobre os autos.
4. O advogado responde ao cliente com segurança.

**2. Audiência marcada com urgência para processo parado há mais de 100 dias**
1. O advogado recebe por e-mail a notificação de agendamento da audiência.
2. Carrega o documento com o andamento atualizado no Insight.
3. O Insight gera o resumo processual e fica disponível para perguntas sobre os autos.
4. O advogado toma ciência do andamento e dos argumentos das partes.

## 3. Escopo e funcionalidades

### F1 — Resumo executivo

- **Descrição:** geração automática de resumo estruturado ao carregar os autos.
- **Regra de negócio:** no máximo 1 página, em linguagem clara e acessível.

### F2 — Chat contextual com provas (Q&A)

- **Descrição:** chat em que o usuário pergunta sobre o processo.
- **Requisito crítico:** toda resposta cita a fonte exata (ex.: "Fls. 45, ID 8920"). Sem evidência nos autos, a IA
  não responde.

### F3 — Timeline de eventos críticos

- **Descrição:** linha do tempo com apenas os marcos processuais.
- **Comportamento:** ignora movimentações de sistema e destaca Decisão, Mandado, Contestação, Despacho, Réplica,
  Laudo, Sentença, Recurso e Acórdão.

### Fora do escopo

- Busca de jurisprudência externa.
- Juízo de valor ou opinião sobre o mérito (neutralidade).
- Informação ausente dos autos: o sistema informa que não encontrou.

## 4. Roadmap

| Fase | Objetivo | Entregáveis |
|---|---|---|
| 1 — MVP | O que está acontecendo agora | Resumo do status atual com citação de fonte (página/ID) |
| 2 — Curto prazo | Aprofundamento | Chat livre (Q&A) para preparação de audiências |
| 3 — Backlog | Expansão de contexto | Análise de jurisprudência e comparação com casos similares |

## 5. FAQ

**A IA pode inventar fatos sobre o processo?**
Não. Pelo Princípio da Não-Invenção, se a informação não estiver nos documentos carregados, o assistente responde
"Não encontrei essa informação nos autos".

**Como confiar na resposta do chat?**
Toda resposta traz a fonte (página e ID do documento) para verificação no documento original.

**O resumo diz quem tem razão?**
Não. Pelo Princípio da Neutralidade, o resumo relata os fatos narrados pelas partes sem opinar sobre o mérito.
