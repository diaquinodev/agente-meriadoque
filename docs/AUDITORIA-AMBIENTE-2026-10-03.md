# Auditoria do ambiente de desenvolvimento — QG `AGENTE-MERIADOQUE`

Data: 2026-10-03 · Branch auditada: `sessao/2026-10-02` · Feita por: Claude (Opus 5.5) no Claude Code

**Escopo:** só este repositório (o QG). Os repositórios de projeto (recebimento-fiscal,
auditor-de-repasses, coach-de-entrevista, dashboard-precificacao) não foram inspecionados.

**Como ler as marcações:** toda afirmação tem origem (arquivo, linha ou saída de comando).
O que foi deduzido está marcado como **[inferência]**.

## Resumo

O processo (memória, skills, fluxo de PR) está funcionando e em uso. A imposição automática
de regras não existe neste repositório: tudo é orientação em texto. Vários arquivos e pastas
citados no roteiro da auditoria (`docs/AGENTS.md`, `docs/memory.md`, `docs/feature_map.md`,
`docs/spec_template.md`, `src/features/`) não existem aqui.

Isso é coerente com um QG que só guarda markdown. Regras de importação, mapa de features e
testes pertencem aos repositórios de projeto, onde há código.

## 1. Estrutura e arquivos de diretrizes

| Arquivo citado no roteiro | Existe? | O que há no lugar |
|---|---|---|
| `docs/AGENTS.md` | Não | `AGENTS.md` na raiz; `CLAUDE.md` contém só `@AGENTS.md` |
| `docs/memory.md` | Não | `brain/` (princípios, lições, planos) |
| `docs/feature_map.md` | Não | Nada equivalente |
| `docs/spec_template.md` | Não | Planos em `brain/plans/<projeto>/` |

Em `docs/` existiam, antes deste relatório, quatro arquivos: `GUIA-DO-AGENTE.md`,
`MANUAL.md`, `PROMPT-ANTI-ALUCINACAO.md` e `PROMPT-CURSOR-SETUP.md`.

O que entra no contexto do agente sozinho, sem ele pedir:

- **`AGENTS.md`**: carregado automaticamente pelo Claude Code via `CLAUDE.md`.
- **`brain/index.md`**: injetado por um hook (script que o Claude Code roda sozinho num
  evento) de início de sessão, definido em `.claude/settings.json` e
  `.claude/hooks/inject-brain.sh`. Ele injeta só o índice, não o conteúdo das notas.
- **`MEMORY.md`**: memória pessoal do Claude Code, fora do repositório
  (`C:\Users\diaqu\.claude\projects\d--AGENTE-MERIADOQUE\memory\`).

O resto (notas do brain, skills, `docs/`) o agente só lê se decidir abrir. Nada obriga.

## 2. Guardrails: hard constraints vs. soft constraints

Hard constraint é a regra imposta por uma ferramenta que faz o comando falhar. Soft
constraint é a regra escrita em texto, que depende de o agente obedecer.

**Hard constraints neste repositório: nenhuma.**

- Não existe a pasta `.github/`, portanto não há CI (verificação automática a cada push).
- Não há hooks de git ativos, nem configuração de linter ou de checagem de tipos.
- A proteção da branch `main` não está disponível. A API do GitHub respondeu
  `403: Upgrade to GitHub Pro or make this repository public to enable this feature`.

**Soft constraints:** todo o `AGENTS.md`, incluindo "nunca commit direto na `main`", o fluxo
brainstorm → plan → backlog e as regras de honestidade.

**Casos intermediários:**

- **CodeRabbit** comenta no PR, mas não bloqueia o merge. Além disso, `.coderabbit.yaml`
  (linhas 6-11) exclui `docs/`, `estudos/`, `brain/`, `todos.md` e todo `*.md`, ou seja,
  quase tudo que este repositório contém.
- **Modo de permissão do Claude Code** pode barrar uma ação do agente, mas é o usuário
  aprovando na hora, não uma regra arquitetural.

Se o agente tentasse importar código proibido ou commitar na `main` aqui, nenhuma
ferramenta bloquearia.

## 3. Grafos de dependência e navegação

- **Dependências entre módulos:** não se aplica. Não existe `src/`, nem código de
  aplicação, nem validador de importações (como dependency-cruiser ou import-linter).
- **`feature_map.md`:** não existe. Rotas e seletores DOM seriam descobertos lendo o código
  do projeto.
- **Flame graphs e DevTools:** o agente sabe interpretar um perfil exportado ou um print
  (onde o tempo se concentra, pilhas largas, funções repetidas). Na sessão da auditoria não
  havia ferramenta conectada a um navegador para capturar um perfil.

## 4. Verificação autônoma e loop de execução

- **Neste repositório** não há testes, linter nem build para rodar. A verificação possível
  é ler o arquivo e conferir a saída de comandos.
- **Em projetos de código**, a skill `execute` manda procurar test runner, linter, type
  checker ou build do projeto e nunca commitar com checagem falhando
  (`.agents/skills/execute/SKILL.md`, linhas 29-34). O princípio
  `brain/principles/prove-it-works.md` reforça. É orientação em texto, não mecanismo.
- **Autocorreção:** o agente lê a falha, corrige e roda de novo antes de avisar. Não existe
  número máximo de tentativas definido em lugar nenhum. Se não conseguir consertar, a regra
  do `AGENTS.md` é relatar a falha com a saída real.
- **Noodle:** configurado em `mode = "supervised"` (`.noodle.toml`, linha 1), com ciclo
  `execute` → `review` descrito nas skills. Não foi rodado nesta auditoria.

## 5. Memória persistente e aprendizado

| Onde | O que guarda |
|---|---|
| `brain/principles/` | 16 princípios gerais |
| `brain/codebase/` | 6 notas de armadilhas técnicas |
| `brain/plans/` | Briefs e planos de 4 projetos |
| `estudos/` | Casos didáticos para o usuário |
| `MEMORY.md` (fora do repositório) | Preferências do usuário |

Sobre ler antes de agir: o índice do brain chega sempre; as notas só são lidas se o agente
abrir. Não existe sistema de evals (testes automáticos de qualidade) no QG.

**Exemplo de erro registrado** (`brain/codebase/code-editing-gotchas.md`, linhas 6-9): um
`s.replace("", novo)` com trecho calculado errado inseriu texto entre todos os caracteres e
gerou um arquivo de 11 MB. Regra anotada: fazer `assert trecho` e
`assert s.count(trecho) == 1` antes de substituir.

## 6. Economia de tokens e roteamento de modelos

- **Leitura restrita a `src/features/<nome>/`:** não, porque a pasta não existe. A prática
  é buscar antes de abrir arquivo inteiro, seguindo
  `brain/principles/guard-the-context-window.md`. É soft constraint.
- **Especificação prévia (`spec.md`):** não existe com esse nome, mas o equivalente existe
  e está em uso. O brainstorm gera um `brief.md` e a skill `plan` gera `overview.md` mais
  fases, em `brain/plans/<projeto>/`. Os quatro projetos têm brief e overview; só o
  auditor-de-repasses tem as fases em arquivos próprios (10 fases).
- **Roteamento de modelos:** existe uma regra escrita em
  `.agents/skills/schedule/SKILL.md` (linhas 35-42):

| Etapa | Modelo |
|---|---|
| Plano, revisão, execução ambígua ou de arquitetura | `claude-opus-5-5` |
| Execução com plano claro | `claude-sonnet-5-5` |
| Docs, texto, edições simples, reflect | `claude-haiku-4-5-20251001` |

  Essa tabela só vale dentro do loop do Noodle. O padrão do Noodle é Sonnet
  (`.noodle.toml`, linhas 3-5). Numa conversa direta, quem escolhe o modelo é o usuário.
- **Modelo leve rodando linter/formatação:** não existe. Linter nem precisa de modelo: é um
  programa comum, e o lugar dele é o CI.

## 7. Fluxo operacional de cada demanda

É o fluxo definido no `AGENTS.md`:

1. **Brainstorm** com o usuário: questionar a ideia e fechar um `brief.md`. Sem código.
2. **Plan:** dividir em fases em `brain/plans/<projeto>/`.
3. **Backlog:** só então a tarefa entra em `todos.md`.
4. **Execução** em branch ou worktree (cópia isolada do projeto), nunca na `main`, com
   testes e linter do projeto rodando antes de cada commit.
5. **Pull Request:** no QG, um PR por sessão; em projeto, um PR draft por fase, marcado
   como pronto só com os testes passando. O usuário faz o merge.
6. **Reflect:** lições vão para o `brain/` e, se didáticas, para `estudos/`.

Na prática, as etapas 1, 2, 5 e 6 têm evidência no repositório: briefs, planos, PRs 7 a 10
mergeados e notas do brain. A etapa 4 pelo loop automático do Noodle não tem: o Noodle está
instalado (`v0.1.5`), mas a pasta `.noodle/` não existe e o `todos.md` ainda tem o
item-placeholder nº 1. **[inferência]** O loop nunca rodou neste repositório; a execução tem
sido feita em conversa direta.

## Diagnóstico

### Funcionando e em uso

- `AGENTS.md` como fonte única de regras.
- Hook que injeta o índice do brain no início da sessão (a saída dele apareceu na sessão
  da auditoria).
- Brain com conteúdo real e skills de brainstorm, plan, reflect e review.
- Fluxo de PR por sessão: quatro PRs mergeados (7, 8, 9 e 10).

### Existe, mas sem prova de funcionamento

- Loop do Noodle e a tabela de roteamento de modelos.
- Hook `auto-index-brain.sh`. **[inferência]** O `matcher: "brain/"` em
  `.claude/settings.json` casa com nome de ferramenta, não com caminho de arquivo, então
  ele provavelmente nunca dispara. O commit `a0c86f1` já fala em "auto-index morto".
- CodeRabbit: está configurado, mas exclui quase todo o conteúdo deste repositório.

### Não existe

- CI, hooks de git, linter de markdown ou de links.
- Proteção da `main` (exige GitHub Pro ou repositório público).
- `feature_map.md`, `spec_template.md`, `src/features/`, validador de importações.

### Recomendação

A maior parte do "não existe" não faz falta no QG. O que valeria a pena aqui é pequeno:

1. Um CI simples que confira links quebrados e wikilinks do brain.
2. Consertar ou apagar o hook de auto-índice.

Qualquer um dos dois é mudança nova e, pelo `AGENTS.md`, passa pelo brainstorm antes.

## O que ficou sem verificação

- Os repositórios de projeto, que podem ter CI, testes e linter próprios.
- O comportamento real do loop do Noodle.
- Se o hook de auto-índice dispara ou não (não foi testado).
- `.agents/skills/noodle/SKILL.md` (linha 52) cita `claude-opus-4-6` num exemplo; não foi
  conferido se é só exemplo ou instrução.

## Comandos usados

- `git ls-files` e `ls -la` na raiz (87 arquivos rastreados).
- Leitura de `.claude/settings.json`, dos dois hooks, `.coderabbit.yaml`, `.noodle.toml`,
  `.gitignore`, `todos.md` e das notas do brain citadas.
- `gh api repos/diaquinodev/agente-meriadoque/branches/main/protection` (resposta `403`).
- `noodle --version` (`v0.1.5`) e `gh pr list --state all --limit 5`.
