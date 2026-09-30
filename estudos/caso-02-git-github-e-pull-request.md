# Caso 02 — Git, GitHub e o primeiro Pull Request

Data: 2026-09-29

## 1. Contexto

Você já perdeu projetos porque eles existiam só no computador. O objetivo aqui foi:
(1) ter backup automático na nuvem e (2) começar a trabalhar como um time de empresa,
onde nada entra na versão oficial sem passar por um Pull Request.

## 2. O que fizemos (em ordem)

| Passo | Comando / ação | Por quê |
|---|---|---|
| 1 | `winget install GitHub.cli` | `gh` = GitHub pelo terminal: cria repositório, issue e PR sem abrir o site. |
| 2 | `gh auth login` (HTTPS + navegador) | O PC recebe um *token* (crachá digital) guardado no cofre do Windows. |
| 3 | `git config --global user.name "Diego Aquino"` | Nome que aparece em todos os commits, de todos os projetos. |
| 4 | `git config --global user.email "<id>+diaquinodev@users.noreply.github.com"` | E-mail *noreply*: o commit conta no seu perfil sem expor seu e-mail real. |
| 5 | `git config --global init.defaultBranch main` | Todo repositório novo já nasce com a branch `main` (padrão do mercado). |
| 6 | `git branch -m master main` | Renomeou a branch deste repositório. |
| 7 | `gh repo create agente-meriadoque --private --source . --push` | Criou o repositório privado no GitHub e enviou tudo. **Backup feito.** |
| 8 | `gh issue create ...` → issue #1 | Em empresa, todo trabalho nasce de um "cartão" registrado. |
| 9 | `git switch -c docs/caso-02-git-github` | Branch de trabalho. A `main` não é tocada. |
| 10 | commit + `git push -u origin <branch>` + `gh pr create` | Abre o Pull Request pedindo para juntar a branch na `main`. |

## 3. Conceitos

- **Git vs GitHub** — Git é o programa de "máquina do tempo" que roda no seu PC.
  GitHub é o site que guarda uma cópia na nuvem e adiciona colaboração (issues, PRs).
  Analogia: Git é o Word com histórico de versões; GitHub é o Google Drive onde você
  compartilha.
- **Remote / origin** — o endereço da cópia na nuvem. `origin` é só o apelido padrão.
- **Push / Pull** — `push` envia seus commits para a nuvem; `pull` traz os commits da
  nuvem para o seu PC.
- **main** — a branch oficial. Em empresa, ela representa "o que está (ou pode ir) em
  produção".
- **Branch** — uma linha paralela de trabalho. Nome com prefixo diz o tipo:
  `feat/` (funcionalidade), `fix/` (correção), `docs/` (documentação).
- **Issue** — cartão de tarefa ou bug. Tem número (#1) e critérios de aceite.
- **Pull Request (PR)** — "Terminei na minha branch, por favor revisem e juntem na
  `main`". Mostra o que mudou (*diff*), permite comentários linha a linha e aprovação.
- **`Closes #1`** — escrito na descrição do PR, fecha a issue automaticamente quando o
  PR é aceito. Liga o "porquê" (issue) ao "como" (PR).
- **Merge** — o ato de juntar a branch na `main`.
- **Squash and merge** — junta todos os commits do PR em um só na `main`. Mantém o
  histórico limpo: um PR = um commit.
- **Token** — credencial que substitui a senha para programas. Nunca cole em chat ou
  código.

## 4. Por que assim

- **Privado primeiro**: o QG tem seu `brain/` e seus estudos, que são pessoais. Tornar
  público depois é um clique; "despublicar" algo que já vazou é impossível.
- **E-mail noreply**: repositórios públicos expõem o e-mail de cada commit, e robôs
  coletam esses e-mails para spam.
- **PR mesmo trabalhando sozinho**: cria o hábito, gera um histórico que explica cada
  mudança e é exatamente o que recrutadores olham no seu GitHub.

### Ajuste de processo: agrupar PRs

No mesmo dia abrimos vários PRs pequenos, e o revisor automático **CodeRabbit** (um robô
de IA que comenta em cada PR) estourou a cota do plano gratuito: "Review limit reached,
next review in 43 minutes". Não bloqueava o merge, mas mostrou um desperdício: o robô
gastava a cota revisando **texto**.

Decisão (registrada no `AGENTS.md`):

| Onde | Regra |
|---|---|
| QG (docs, brain, estudos) | 1 branch `sessao/AAAA-MM-DD`, 1 commit por assunto, **1 PR por sessão** |
| Projetos de código | 1 PR **por fase do plano**, aberto como **draft** e marcado *Ready for review* só quando pronto |
| CodeRabbit | `.coderabbit.yaml` ignora `docs/`, `estudos/`, `brain/` e `.md`; não revisa drafts |

**Trade-off** (troca: o que se ganha e o que se perde): no QG, o *squash* transforma o
dia inteiro em um commit só na `main`, e perdemos o histórico de cada mudança separada.
Para documentação vale a pena; para código, não, por isso lá o PR continua sendo por fase.

Lição de engenharia: **processo tem custo**. Um bom processo dá o controle que você
precisa pelo menor custo (tempo, cota, atenção). Quando o processo começa a atrapalhar,
ajuste o processo; não o abandone.

## 5. Hacks e pegadinhas

- `git status -sb` mostra a branch atual e se você está à frente ou atrás da nuvem.
- Sempre confira em qual branch está antes de commitar. Commit na `main` por engano:
  crie a branch a partir dali (`git switch -c nome`) antes de dar push.
- Você não consegue "aprovar" o próprio PR no GitHub. Isso é proposital: em empresa,
  quem escreve não aprova. Sozinho, você revisa o *diff* e faz o merge.
- Depois do merge, atualize o PC: `git switch main` e `git pull`. E apague a branch
  antiga (o GitHub oferece um botão para isso).
- Em repositório **privado** no plano gratuito, as regras de proteção da `main`
  (bloquear push direto) não estão disponíveis. Por enquanto a proteção é a disciplina
  (e a regra no `AGENTS.md`).

## 6. O que estudar (prioridade)

1. Ciclo diário: `status → add → commit → push → pull`.
2. Branches e merge; o que é um **conflito** e como resolver.
3. Pull Requests: escrever uma boa descrição e revisar um *diff*.
4. Conventional Commits (padrão das mensagens).
5. GitHub Actions (CI): o robô que testa cada PR.

## 7. Perguntas para o NotebookLM

1. Qual a diferença entre Git e GitHub?
2. O que acontece com a issue #1 quando o PR com `Closes #1` sofre merge?
3. Por que não se faz commit direto na `main`?
4. Para que serve o e-mail noreply?
5. Qual a diferença entre *merge commit* e *squash and merge*?
6. Depois do merge, que dois comandos deixam meu PC atualizado?
7. Por que o GitHub não deixa eu aprovar meu próprio PR?
8. Por que agrupar PRs no QG, mas não nos projetos de código?
9. O que é um PR em modo *draft* e por que ele economiza revisões?

## 8. Referências

- Pro Git (livro oficial e gratuito): https://git-scm.com/book
- GitHub Docs: https://docs.github.com
- Conventional Commits: https://www.conventionalcommits.org
