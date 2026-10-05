# Coach de Estudos

Brainstorm em 2026-10-05. Origem: o usuário tem projetos publicados e não domina o que foi
feito; trava para começar a estudar pela quantidade de assunto e pelo medo de focar no
lugar errado.

- **Problem:** não consegue começar nem manter o foco (liga o PC e faz várias coisas ao
  mesmo tempo); não sabe o que estudar; não tem o hábito de escrever e não consegue anotar
  com as próprias palavras. Está desempregado e mandando currículo: o estudo precisa
  aumentar a chance de passar em entrevista.
- **Users:** o usuário. Vagas-alvo: Analista de IA/Dados e Analista de Dados/IA com
  automação e melhoria de processos. Objetivo de longo prazo: engenharia de IA.
- **Rotina disponível:** segunda a sábado, 4h–9h (cochilo de até 40 min) e 13h30–17h.
  Dorme 21h30, acorda 3h30. Entre 9h e 13h cuida do filho e do almoço.
- **Decisão central:** os **cases** (projetos do GitHub, documentados em `estudos/`) são o
  centro do estudo. Fundamentos (SQL, Python, modelagem, testes) entram puxados pelo case.
  A lista do que estudar sai dos projetos e das vagas, não de um currículo genérico.
- **MVP scope (in):**
  1. **Skill `coach-de-estudos`** no QG com três rituais:
     - **planejar** (fim do dia): monta a página "Hoje" de amanhã, um objetivo por bloco;
     - **sessão**: conduz um bloco de 50 min e dita a página do caderno no nível de
       escrita do assunto, depois faz 3 perguntas de memória e corrige;
     - **revisar** (sábado): o que ficou, o que sobe de nível, o que muda na semana.
  2. **Painel no Notion** (conta já conectada): página "Hoje", semana, trilha dos cases com
     o nível de escrita de cada um, fila de leitura e vídeo, sequência de dias.
  3. **Escrita com apoio decrescente**, registrada por assunto: nível 1 copiar e
     acrescentar uma frase própria; nível 2 completar lacunas; nível 3 responder de memória
     e conferir; nível 4 explicar em página em branco.
  4. **NotebookLM, um caderno por case**, com prompt que restringe as respostas às fontes
     e pede citação; uso de flashcards, mapa mental, áudio e relatório.
  5. **Fila de leitura e vídeo curada**, com link verificado: Fabio Akita, Lauren Tan,
     Fernanda Kipper, Sandeco, Andre Okazaki, Simon Willison, Latent Space. Um item por
     dia, sempre com uma página de anotação.
  6. **Orçamento do dia:** 3 blocos de case + 1 h de prática de manhã; à tarde, vagas,
     entrada (vídeo ou artigo) e fechamento. Projeto novo em 2 tardes por semana.
- **Out of scope (later):** alertas e pomodoro no celular pelo bot do Telegram (etapa 2,
  depois de 2 semanas de uso); bot com RAG sobre os projetos (vale como projeto de
  portfólio de engenharia de IA, não como ferramenta de estudo agora); trilha de negócios
  como frente separada (entra pela fila de leitura); faculdade (matrícula trancada por
  pagamento; volta ao plano quando reabrir); cursos da DSA assistidos em segundo plano
  (parar); aplicativo próprio com agenda e métricas.
- **Chosen approach and why:** skill no QG + Notion + NotebookLM + caderno de papel. Nada
  de código novo: o risco maior é o sistema de estudo virar a nova forma de não estudar.
  Precisa estar em uso no dia seguinte.
- **Rejected alternatives and why:** aplicativo próprio (semanas de construção antes de
  estudar); bot do Telegram com RAG agora (o NotebookLM já responde pelas fontes, de
  graça); o coach ditar tudo para copiar sempre (copiar é passivo; fica só como nível 1,
  com plano de retirada do apoio); diário só em arquivo de texto (o usuário pediu
  organização visual e acesso pelo celular).
- **Regras do método:**
  - Um bloco, um objetivo escrito, uma tela.
  - Só publica projeto novo quem consegue explicar o anterior sem consultar.
  - Verificação do próprio estudo: 3 perguntas de memória por bloco (o equivalente ao
    `conferir.ps1` dos projetos).
  - Regra dura antes de regra mole: ajustar o ambiente (abas, celular) em vez de prometer
    foco.
- **Stack:** Markdown (skill em `.agents/skills/coach-de-estudos/`), conector do Notion,
  NotebookLM, caderno de papel.
- **Verification (lint/types/tests):** não há código. A verificação é de uso: a skill é
  testada conduzindo uma sessão real; o prompt do NotebookLM é testado com 3 perguntas
  cuja resposta está nas fontes e 1 que não está (deve dizer que não sabe).
- **Success criteria (experimento de 2 semanas):**
  1. Dias em que sentou às 4h: 10 de 12 ou mais.
  2. Blocos de case concluídos: 24 de 36 ou mais.
  3. Um case explicado no nível 4 (página em branco) sem consultar.
  4. Uma página de caderno por bloco, todos os dias de estudo.
- **Open questions:**
  - Ordem dos cases. Proposta: inteligencia-de-compra primeiro (SQL, BigQuery, Power BI,
    qualidade de dados: o mais próximo das vagas), depois recebimento-fiscal e
    conciliacao-bancaria (automação e processo).
  - Sono de 6 h mais cochilo: observar o foco na primeira semana.
  - Conector do Notion: confirmar que a skill consegue criar e atualizar as páginas.
  - Qual vídeo do canal do Andre Okazaki entra primeiro na fila.
- **Relacionados:** [[coach-de-entrevista/brief]] (treino de entrevista: é onde o estudo
  dos cases é posto à prova), [[inteligencia-de-compra/overview]].

**Não verificado:** a base de pesquisa citada no brainstorm (Dunlosky 2013 sobre técnicas
de estudo; Sweller e Renkl sobre exemplos resolvidos com apoio decrescente) é de memória,
sem fonte aberta na sessão. Confirmar antes de citar em material de estudo.
