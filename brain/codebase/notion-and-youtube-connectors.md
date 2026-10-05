# Conectores: Notion e YouTube (vidIQ)

Aprendido em 2026-10-05, montando o coach de estudos ([[plans/coach-de-estudos/overview]]).

## Notion

- **Procurar antes de criar.** `notion-search` com uma palavra do tema mostrou um painel
  de estudos já existente. Medir uso lendo as linhas dos bancos, não só a página.
- **Banco de dados:** `fetch` na página devolve a tag `<database ... data-source-url=
  "collection://...">`; `fetch` nesse `collection://` devolve o esquema e os nomes exatos
  das colunas. Só então consultar ou criar linhas.
- **Criar linhas:** `create-pages` com `parent.data_source_id`; data em três chaves
  (`date:Data:start`, `date:Data:is_datetime`); caixa de seleção `__YES__` / `__NO__`.
  O corpo da linha aceita o mesmo Markdown da página (títulos, blocos de código).
- **Trocar o conteúdo de uma página com bancos e subpáginas:** `replace_content` copiando
  as tags `<database ...>` e `<page ...>` exatamente como vieram no `fetch`; sem elas a
  ferramenta recusa por apagar filhos. Reler a página depois para conferir.
- **Custo de contexto:** a definição da ferramenta de consulta (`query-data-sources`) é
  enorme. Carregar só quando for consultar linhas de verdade.
- **O agente só enxerga o Notion quando o usuário abre uma sessão.** Não há verificação
  em segundo plano; o fluxo precisa de um gatilho do usuário ("revisão da semana").

## YouTube

- **`WebFetch` numa URL do YouTube não devolve título nem descrição** (só o rodapé).
- **Transcrição:** `vidiq_video_transcript` com o ID do vídeo. Custa 5 créditos da conta
  do usuário por chamada; uma palestra de 1 h dá cerca de 50 mil caracteres.
- **Link de canal não é vídeo:** `vidiq_channel_videos` (5 créditos) lista os envios;
  escolher por título é escolha por título, e isso deve ser dito ao usuário.
- **Afirmações de palestra são da palestrante.** Números e nomes de produto citados em
  vídeo não são fato verificado; relatar como "a palestra diz".

Related: [[codebase/agent-tool-portability]], [[principles/prove-it-works]]
