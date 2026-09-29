# AGENTE-MERIADOQUE — Regras para qualquer agente

Vale para Claude (Claude Code / AntiGravity) e Gemini (AntiGravity / Gemini CLI).
Este arquivo é a fonte única de regras. Não crie cópias dele (`GEMINI.md`, `rules.md` etc.).

## O que é este repositório

O "QG" de engenharia do usuário: skills, memória (`brain/`), documentação e estudos.
Os projetos reais (automações, sites, chatbots, extração de dados, telas de app)
vivem em repositórios próprios, criados a partir daqui.

## Quem é o usuário

Estudante de Engenharia de IA e automação, não programador. Aprender com o processo
é tão importante quanto entregar.

- Explique o *porquê* de cada decisão técnica em linguagem simples.
- Termos técnicos: use, mas explique na primeira vez (ex: "worktree (uma cópia
  isolada do projeto para trabalhar sem mexer no original)").
- Quando algo relevante for aprendido, registre em `estudos/` (ver `estudos/README.md`).

## Fluxo de trabalho

1. **Brainstorm** (skill `.agents/skills/brainstorm/`) — interativo, com o usuário. Questionar, confrontar e melhorar a ideia antes de qualquer código.
2. **Plan** (skill `plan`) — transformar a conclusão em plano em fases (`brain/plans/`).
3. **Backlog** — só então a tarefa entra em `todos.md`.
4. **Execução** — loop do Noodle (`execute` → `review`), em branch/worktree, nunca direto na `main`.
5. **Reflect** — registrar aprendizados em `brain/` e, se didático, em `estudos/`.

Nunca pule direto para o código numa ideia nova sem passar pelo brainstorm.

## Onde fica cada coisa

| Pasta/arquivo | Para quê |
|---|---|
| `brain/` | Memória persistente do projeto (princípios, conhecimento, planos). Leia `brain/index.md` antes de agir. |
| `.agents/skills/<nome>/SKILL.md` | Instruções de cada habilidade. Se a tarefa combina com uma skill, leia o `SKILL.md` dela e siga. |
| `docs/` | Documentação para o usuário (guia do agente). |
| `estudos/` | Estudos de caso didáticos (vão para o NotebookLM). |
| `todos.md` | Backlog do Noodle. |
| `.noodle/` | Estado interno do Noodle — nunca edite à mão. |

## Regras do brain

- **Leia primeiro** os arquivos do brain relevantes à tarefa.
- **Escreva** depois de erros, correções ou aprendizados importantes.
- **Estrutura:** um tema por arquivo; índices com `[[wikilink]]`, sem conteúdo inline.
- **Manutenção:** apague notas desatualizadas.

## Não faça

- Não crie arquivos fora da estrutura acima sem combinar com o usuário.
- Não duplique regras deste arquivo em outros lugares.
- Não faça commit direto na `main` de um projeto; use branch + Pull Request.
- Agentes que não são Claude (ex: Gemini) não iniciam o loop do Noodle (`noodle start`).
