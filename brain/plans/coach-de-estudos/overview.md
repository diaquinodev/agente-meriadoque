# Coach de Estudos — Plano

Brief: [[coach-de-estudos/brief]]

## Contexto

O usuário não consegue começar a estudar nem manter o foco, e não domina os projetos que
publicou. Está desempregado; o estudo precisa aumentar a chance de passar em entrevista para
Analista de IA/Dados. O centro do estudo são os cases em `estudos/`.

### Achado que muda o plano (2026-10-05)

Já existe no Notion um **"Painel de Estudos — AI Engineer"**, criado em 2026-10-01
(https://app.notion.com/p/3ece54345b9a811e8ebed6244ea720b4), com:

- rotina do dia, método do caderno (cores, símbolos de fluxograma, formato da página);
- banco **Lições** (6 lições da semana 1, sobre LLM, prompt, tool use, agente, JSON);
- banco **Diário** (Fiz / Travei em / Amanhã começo por) e banco **Dúvidas**;
- **Prompt mestre do NotebookLM**, com regras de só responder pelas fontes e formato fixo;
- links para um "Caderno Guiado — Semana 1" e um "Roadmap AI Engineer — 6 meses".

**Uso medido em 2026-10-05:** Diário com zero linhas; as 6 lições em "A fazer", nenhuma
marcada. Entre 02/10 e 04/10 o tempo foi para construir o inteligencia-de-compra.

Conclusão: o problema não é falta de sistema. Um segundo sistema construído do zero teria
o mesmo destino. O plano parte do painel que existe, corta o que não serve e põe a
**primeira sessão real** antes de qualquer construção.

## Escopo

**Dentro:** ajustar o painel existente; um caderno do NotebookLM para o primeiro case; uma
sessão piloto conduzida no chat; skill `coach-de-estudos` escrita a partir do piloto; fila
de leitura e vídeo; duas semanas de uso com revisão aos sábados.

**Fora:** painel novo; alertas pelo Telegram; bot com RAG; aplicativo próprio; faculdade
(trancada); cursos da DSA em segundo plano.

## Restrições

- **Nada de código novo.** Notion (conector), NotebookLM (manual: não tem API; o agente
  prepara e o usuário cola), skill em Markdown, caderno de papel.
- **Rotina real:** segunda a sábado, 4h–9h (cochilo de até 40 min) e 13h30–17h; 9h–13h é
  do filho e do almoço. A rotina do painel antigo (construir das 9h10 às 12h) não cabe.
- **Não apagar conteúdo do usuário no Notion** sem ele confirmar; o que sair é arquivado.
- **Alternativas consideradas:** (a) painel novo no Notion — rejeitada pelo achado acima;
  (b) diário em arquivo no QG — rejeitada, o usuário pediu visual e celular; (c) ajustar o
  painel existente — escolhida.
- **Ordem dos cases:** inteligencia-de-compra (`estudos/caso-11`), depois
  recebimento-fiscal (`caso-09`) e conciliação bancária.

## Skills aplicáveis

- `brain` (convenções de escrita), `reflect` (revisão de sábado alimenta o brain).
- Fase 4 cria uma skill: usar `skill-creator` se estiver instalada; se não, seguir o
  formato das skills em `.agents/skills/`.

## Fases

Plano pequeno, em arquivo único. Cada fase termina com algo que o usuário usa.

### Fase 1 — Ajustar o painel existente (subtrair antes de somar)

- **Objetivo:** o painel refletir a rotina real e o estudo por cases.
- **Mudanças (Notion):** trocar a tabela de rotina pela do brief; trocar o aviso "Comece
  às 9h" por "Comece às 3h50"; arquivar as 6 lições genéricas e criar as 6 primeiras do
  case inteligencia-de-compra (uma por bloco, a partir das seções de `caso-11`); em
  **Lições**, acrescentar o campo "Nível de escrita" (1 a 4) e "Case"; em **Diário**,
  acrescentar "Sentei às 4h" (caixa) e "Blocos feitos" (número). Ler os dois materiais
  ligados (Caderno Guiado, Roadmap) e decidir com o usuário se ficam.
- **Estruturas:** Lição = título, case, data, nível de escrita, Anotei, Desenhei,
  Pratiquei, Confiança. Dia = data, sentei às 4h, blocos feitos, fiz, travei, amanhã.
- **Verificação:** reler a página e os bancos pelo conector e conferir os campos e as 6
  lições datadas; o usuário abre no celular e confirma que lê bem.

### Fase 2 — NotebookLM do primeiro case

- **Objetivo:** um caderno que responde só pelas fontes do inteligencia-de-compra.
- **Mudanças:** lista de fontes (caso-11, README, `docs/*.md` e `powerbi/DESIGN.md` do
  repositório); versão do prompt mestre com exemplos do próprio case no lugar dos de
  e-commerce; página do painel atualizada com o prompt.
- **Verificação:** 3 perguntas cuja resposta está nas fontes (resposta com citação) e 1
  que não está (deve responder "Isso não está nas fontes deste notebook").

### Fase 3 — Sessão piloto (primeiro bloco real)

- **Objetivo:** um bloco de 50 min conduzido no chat, antes de existir skill.
- **Mudanças:** nenhuma em arquivo antes da sessão. Durante: objetivo do bloco, página do
  caderno no nível 1, 3 perguntas de memória, linha no Diário.
- **Verificação:** linha do dia no Diário preenchida; lição marcada (Anotei, Desenhei);
  nota de confiança; lista do que travou e do que o coach fez de errado.

### Fase 4 — Skill `coach-de-estudos`

- **Objetivo:** os três rituais (planejar, sessão, revisar) escritos a partir do que o
  piloto mostrou, não de suposição.
- **Mudanças:** `.agents/skills/coach-de-estudos/SKILL.md`; referência em `AGENTS.md` só
  se a tabela de pastas pedir.
- **Estruturas:** nível de escrita por assunto (1 copiar e acrescentar uma frase; 2
  completar; 3 lembrar; 4 explicar); página "Hoje"; formato fixo da página do caderno
  (reusar o do painel).
- **Verificação:** conduzir uma segunda sessão seguindo só o texto da skill; conferir que
  o Diário e a Lição foram atualizados sem intervenção fora do roteiro.

### Fase 5 — Fila de leitura e vídeo

- **Objetivo:** um item por dia, com link verificado e motivo.
- **Mudanças:** banco "Fila" no painel (título, autor, link, tipo, motivo, status).
  Primeiros itens: Andre Okazaki, "Perdido no quê estudar, dev? Eu começaria por esse
  caminho" (escolhido pelo título; conteúdo ainda não lido); os quatro artigos do Akita
  listados no brainstorm; Simon Willison e Latent Space. A palestra da Lauren Tan já foi
  lida e vira a primeira página de anotação.
- **Verificação:** cada link aberto e conferido antes de entrar; nenhum item sem fonte.

### Fase 6 — Semana 1 de uso e revisão de sábado

- **Objetivo:** seis dias seguidos de uso, com medição.
- **Verificação:** contar no Diário os dias com "Sentei às 4h" e os blocos feitos;
  revisão de sábado registra o que sobe de nível e o que muda na semana 2.

### Fase 7 — Fechamento das 2 semanas

- **Objetivo:** comparar com os critérios do brief e decidir o próximo passo (alertas no
  Telegram, segundo case, ou corte de algo que não foi usado).
- **Verificação:** os quatro critérios de sucesso medidos com os dados do Diário e das
  Lições; aprendizados no brain e caso de estudo se houver aprendizado real.

## Verificação geral

Não há lint nem teste: não há código. A prova é de uso, lida direto na fonte:

- contagem de linhas e campos do Diário e das Lições pelo conector do Notion;
- teste do NotebookLM com perguntas dentro e fora das fontes;
- páginas do caderno de papel (o usuário confirma; foto opcional).

## Perguntas em aberto

- Por que o painel de 01/10 não foi usado? A resposta do usuário ajusta as fases 1 e 4.
- Sono de 6 h mais cochilo: observar o foco na primeira semana.

## Status

- 2026-10-05 — brief e plano escritos. Nada executado.
- 2026-10-05 — Fluxo simplificado a pedido do usuário: um arquivo de `estudos/` por semana, uma lição por dia; o agente escreve o prompt do dia, o NotebookLM gera a aula, o Notion registra. Criadas 5 lições do caso 11 no banco Lições (06 a 10/10), cada uma com o prompt do dia. As 6 lições genéricas antigas continuam lá (não apagadas). Pendente (usuário): criar o caderno do caso 11 no NotebookLM, colar o prompt mestre, fazer a lição de 06/10.
