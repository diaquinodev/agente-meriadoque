# Agent tool portability (Claude Code, Antigravity, Cursor)

O núcleo do QG é portátil; os hooks não. Testado no Cursor em 2026-10-02 (caso-10).

- **Portátil, sem configuração:** `AGENTS.md` (o Cursor também lê o `CLAUDE.md`, como o texto
  literal `@AGENTS.md`), skills em `.agents/skills/` (o Cursor as achou pela junction
  `.claude/skills`, sem duplicar), `brain/`, `todos.md`. O Noodle independe da IDE.
- **Hooks não são padrão:** o Cursor importa `.claude/settings.json` (Settings → Agents →
  Third-Party Imports), mas o `sessionStart` dele espera JSON `{"additional_context": ...}`.
  O `inject-brain.sh` imprime texto e não chega ao contexto do Cursor. Não mudar: o
  `AGENTS.md` já manda ler `brain/index.md`. Regra crítica fica em arquivo, nunca só em hook.
- **Matcher de `PostToolUse` casa com o nome da ferramenta** (`Write`, `Edit`), não com o
  caminho. O `auto-index-brain.sh` (matcher `"brain/"`) nunca disparou em nenhuma ferramenta;
  se disparasse, achataria o índice curado. Decisão pendente: remover (ver caso-10).
- **Plugins de terceiros competem com o fluxo:** o pstack (Cursor) traz skills com nomes dos
  nossos princípios e o subagent "Comment Sicko". Não usar `/poteto-mode` no QG.
- **Skills "User" no Cursor** vêm de `~/.claude/skills/synced/` (skills da Anthropic). As que
  dependem de ferramentas do app Claude (computer-use, browsers) não funcionam no Cursor
  [inferência].
- **Uma ferramenta por branch de cada vez.** Relatório de um agente vale ser conferido por outro
  no git e nos scripts.

Related: [[codebase/windows-toolchain-gotchas]], [[principles/subtract-before-you-add]]
