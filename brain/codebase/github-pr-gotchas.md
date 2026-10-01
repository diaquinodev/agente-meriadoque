# GitHub e PR gotchas

Regras de fluxo (quantos PRs, draft) ficam no `AGENTS.md`. Aqui ficam as armadilhas.

- **PRs empilhados (cada fase com base na anterior):**
  - Merge com *Create a merge commit*, não squash — senão a fase seguinte conflita.
  - Ordem a cada merge: merge → **trocar a base do próximo PR para `main`** → só então
    apagar a branch. Apagar antes faz o GitHub **fechar** o próximo PR (aconteceu no
    Auditor, PR #2; recuperado recriando a branch e reabrindo). Ver caso-04.
- **Nunca apagar branch local antes de `gh pr view <n> --json state` = `MERGED`.**
  O usuário às vezes acha que fez o merge e faltou o "Confirm squash and merge".
- **CodeRabbit (plano gratuito)** tem cota por hora; o aviso "Review limit reached" /
  "espere 40 min" vem dele, não do GitHub, e não bloqueia o merge.
- **Proteção de branch** (rulesets) exige GitHub Pro ou repositório público; nos repos
  privados a proteção da `main` é disciplina de PR.
- **Repositório renomeado** (`coach-de-entrevista` → `projeto-pessoal`): o GitHub
  redireciona links antigos, mas atualizar `git remote` e referências no QG.
