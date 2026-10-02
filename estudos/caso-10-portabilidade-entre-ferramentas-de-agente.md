# Caso 10 — Portabilidade: levando o QG do Antigravity para o Cursor

Data: 2026-10-02 · Repositório: `diaquinodev/agente-meriadoque` (o próprio QG)

## 1. Contexto

O QG foi montado no Antigravity com Claude Code e Gemini. Com o Cursor instalado, surgiu a
dúvida: o fluxo (brainstorm → plan → backlog → Noodle → reflect) funciona nele? É preciso
configurar algo, ou até criar um fluxo separado para não quebrar o atual?

## 2. O que fizemos (em ordem)

| Passo | O que aconteceu | Prova |
|---|---|---|
| Pesquisa | documentação do Cursor: lê `AGENTS.md`, `CLAUDE.md` e skills de `.agents/skills/` e `.claude/skills/`; roda hooks do Claude Code se a importação de terceiros estiver ligada | links na seção 8 |
| Prompt mestre | `docs/PROMPT-CURSOR-SETUP.md`: diagnóstico primeiro, mudança só com aprovação, prova para cada item | o arquivo |
| Telas do Cursor | aba *Subagents*: 2 agentes do plugin **pstack** (de poteto, autor do Noodle). Aba *Skills*: grupo "User 13" = skills da Anthropic sincronizadas em `~/.claude/skills/synced/` | pastas conferidas no disco |
| Fase 1 (o Cursor se diagnosticou) | regras OK, 10 skills sem duplicar, terminal OK; os **dois hooks** com problema | tabela do Cursor, conferida pelo Claude |
| Conferência cruzada | o Claude checou as afirmações do Cursor no git e nos scripts | ver seção 5 |

Resultado: **o fluxo funcionou no Cursor sem configuração extra**. Só a camada de hooks falhou,
e o pior defeito dela já existia antes, também no Claude Code.

Pendente nesta data: Fase 2 (propostas de ajuste) — recomendado remover o hook
`auto-index-brain`, manter o `inject-brain` como está e corrigir a skill `plan`.

## 3. Conceitos

- **Portabilidade** — o quanto algo funciona igual em ferramentas diferentes. Analogia: um
  arquivo PDF abre em qualquer leitor; um arquivo salvo no formato próprio de um programa, não.
- **Padrão aberto** — formato público que várias empresas adotam. `AGENTS.md` (regras) e
  Agent Skills (`SKILL.md`) são padrões abertos; por isso o Cursor leu o QG sem ajuste.
- **Hook** — script que a ferramenta roda sozinha em certos momentos (ex.: ao abrir a
  conversa). Não é padrão aberto: cada ferramenta tem seu formato, e o Cursor só traduz o do
  Claude em parte.
- **Matcher** — o filtro que decide quando um hook roda. No `PostToolUse`, ele é comparado com
  o **nome da ferramenta** (`Write`, `Edit`), não com o caminho do arquivo.
- **Junction / link simbólico** — um "atalho de pasta". `.claude/skills` aponta para
  `.agents/skills`: uma pasta real, dois caminhos.
- **Plugin / subagent** — pacote de terceiros que adiciona skills e "agentes ajudantes" ao
  editor. Útil, mas pode competir com o fluxo do projeto.
- **Adaptador** — a parte pequena e específica de cada ferramenta (configuração, hooks) em volta
  de um núcleo que vale para todas.

## 4. Por que assim

- **Não abandonar o Cursor nem criar outro fluxo.** O núcleo do QG (regras, skills, brain) é
  feito de padrões abertos e arquivos Markdown — passou no teste. Criar um fluxo paralelo
  duplicaria regras, e cópias divergem.
- **Diagnóstico antes de mudança.** Medir primeiro evitou "consertar" o que já funcionava.
- **Regra crítica fica em arquivo, não em hook.** "Leia o `brain/index.md` antes de agir" está
  no `AGENTS.md`; por isso, mesmo com o hook `inject-brain` falhando no Cursor, o agente leu o
  brain. Hook é conveniência, não garantia.
- **Subtrair antes de somar.** O hook `auto-index-brain` não faz nada hoje e, se funcionasse,
  estragaria o índice. Remover é melhor que consertar.
- **Papéis, não ferramenta oficial** [inferência, prática comum]: editor para conversar e
  editar com supervisão; CLI (Claude + Noodle) para execução autônoma; CodeRabbit como revisor
  independente. Uma ferramenta por branch de cada vez.

## 5. Hacks e pegadinhas

- **Defeito escondido aparece na troca de ferramenta.** O matcher `"brain/"` nunca casou com
  `Write`/`Edit`, então o `auto-index-brain` **nunca rodou**, nem no Claude Code. Prova: o
  `brain/index.md` mostra `[[codebase]]` como link único (o script listaria cada arquivo) e só
  mudou em 2 commits, ambos feitos à mão.
- **Um conserto pode piorar.** Se o matcher fosse corrigido, o script reescreveria o índice
  inteiro como lista plana, desfazendo a organização feita à mão. Leia o script antes de
  consertar.
- **Instrução falsa em skill.** `plan/SKILL.md` dizia que o auto-index mantém o índice. Uma
  documentação que confia em algo quebrado engana os próximos agentes.
- **Saída de hook diferente por ferramenta.** O Claude Code aceita texto simples no
  `SessionStart`; o Cursor espera JSON (`additional_context`). O mesmo script funciona em um e
  não no outro.
- **Plugins chegam com opinião.** O pstack traz skills com nomes iguais aos princípios do
  `brain/` e um subagent que apaga comentários — competem com o fluxo e com o objetivo de
  aprender lendo o código.
- **Confira o agente com outro agente.** O relatório do Cursor foi checado pelo Claude no git e
  nos scripts; acertou, e ainda faltou um ponto (a skill `plan`) que só a conferência achou.

## 6. O que estudar (prioridade)

1. Padrões abertos de agentes: `AGENTS.md`, Agent Skills (`SKILL.md`), MCP — o que cada um cobre.
2. Hooks: eventos, matchers e formato de saída no Claude Code e no Cursor.
3. Arquitetura "núcleo + adaptadores" (também chamada de portas e adaptadores).
4. Git worktree e como rodar vários agentes em paralelo sem conflito.
5. Como avaliar um plugin de terceiros antes de deixá-lo ativo num projeto.

## 7. Perguntas para o NotebookLM

1. Por que as regras e as skills do QG funcionaram no Cursor sem nenhuma configuração?
2. Qual a diferença entre colocar uma regra no `AGENTS.md` e garanti-la por um hook?
3. Por que o hook `auto-index-brain` nunca rodou, nem no Claude Code?
4. Por que corrigir o matcher desse hook poderia piorar o projeto?
5. O que significa "núcleo portátil + adaptadores finos"? Dê um exemplo do QG.
6. Que riscos um plugin como o pstack traz para um projeto que já tem fluxo próprio?
7. Por que é útil um agente conferir o relatório de outro agente?

## 8. Referências

- Cursor — Agent Skills: https://cursor.com/docs/skills
- Cursor — Using Agent in CLI (AGENTS.md e CLAUDE.md): https://cursor.com/docs/cli/using
- Cursor — Third Party Hooks: https://cursor.com/docs/reference/third-party-hooks
- Agent Skills (padrão aberto): https://agentskills.io/
- AGENTS.md: https://agentic-ai.readthedocs.io/en/latest/Standards/agents-md/
- Noodle (poteto): https://github.com/poteto/noodle
