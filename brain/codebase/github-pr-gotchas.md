# GitHub e PR gotchas

Regras de fluxo (quantos PRs, draft) ficam no `AGENTS.md`. Aqui ficam as armadilhas.

- **PRs empilhados (cada fase com base na anterior):**
  - Merge com *Create a merge commit*, não squash — senão a fase seguinte conflita.
  - Ordem a cada merge: merge → **trocar a base do próximo PR para `main`** → só então
    apagar a branch. Apagar antes faz o GitHub **fechar** o próximo PR (aconteceu no
    Auditor, PR #2; recuperado recriando a branch e reabrindo). Ver caso-04.
  - A doc diz que o GitHub redireciona o próximo PR ao apagar a branch pelo site
    ([changelog](https://github.blog/changelog/2020-05-19-pull-request-retargeting/)), mas
    há bug aberto em que ele fecha ([cli/cli#14223](https://github.com/cli/cli/issues/14223)).
    Não confiar no automático: trocar a base à mão primeiro.
  - **Só mergear o PR cuja base é `main`.** Em 2026-10-02 (recebimento-fiscal) o #1 entrou
    por squash e #3/#5/#7 foram mergeados nas branches intermediárias: #2/#4/#6 ficaram em
    conflito. Conserto sem perda: na branch mais completa, `git merge -s ours origin/main`
    (conferir `git diff` vazio contra o testado), trocar a base para `main` e fechar os PRs
    intermediários com comentário.
- **CI de `pull_request` não roda enquanto o PR tem conflito**, e trocar a base (`edited`)
  não dispara a CI. Prova alternativa: `git diff` vazio contra uma branch já verde.
- **Nunca apagar branch local antes de `gh pr view <n> --json state` = `MERGED`.**
  O usuário às vezes acha que fez o merge e faltou o "Confirm squash and merge".
- **CodeRabbit (plano gratuito)** tem cota por hora; o aviso "Review limit reached" /
  "espere 40 min" vem dele, não do GitHub, e não bloqueia o merge.
- **Proteção de branch** (rulesets) exige GitHub Pro ou repositório público; nos repos
  privados a proteção da `main` é disciplina de PR.
- **Repositório renomeado** (`coach-de-entrevista` → `projeto-pessoal`): o GitHub
  redireciona links antigos, mas atualizar `git remote` e referências no QG.
