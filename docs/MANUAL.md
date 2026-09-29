# Manual do Agente Meriadoque

Passo a passo para usar o agente do começo ao fim de um projeto, e a lista de
comandos para você ganhar domínio das ferramentas.

> **Como ler este manual:** a Parte 1 é o **roteiro** (o que fazer, em que ordem).
> A Parte 2 são os **comandos**. A Parte 3 são **frases prontas** para pedir as coisas.
> Para entender os conceitos (o que é branch, skill, worktree...), veja `docs/GUIA-DO-AGENTE.md`.

---

# Parte 1 — O roteiro de um projeto

```
0. Preparar   →  1. Conceber   →  2. Alinhar    →  3. Planejar
                                                      ↓
7. Aprender   ←  6. Publicar   ←  5. Revisar    ←  4. Construir
```

## Etapa 0 — Preparar o ambiente (todo dia)

1. Abra o **AntiGravity** na pasta do projeto.
2. Abra o terminal embutido: menu **Terminal → New Terminal**.
3. Atualize seu PC com o GitHub:
   ```
   git switch main
   git pull
   ```
   *Por quê:* se algo mudou no GitHub (um merge que você fez pelo site), seu PC fica igual.
4. Escolha com quem conversar:
   - **Claude** (painel de chat ou `claude` no terminal): pensar, planejar, construir, revisar.
   - **Gemini** (`agy` no terminal): segunda opinião, pesquisa, textos rápidos.

## Etapa 1 — Conceber a ideia (brainstorm)

**Objetivo:** transformar "tenho uma ideia" em "sei exatamente o que vou construir e por quê".

1. Diga ao agente: **"Tenho uma ideia: ..."** (ou `/brainstorm`).
2. O agente vai **perguntar** (problema, para quem, como é feito hoje, o que é "pronto").
   Responda com calma. Se não souber, diga "não sei, me ajude a pensar".
3. O agente vai **contestar** e mostrar **2 ou 3 caminhos** diferentes, com custo e o
   que você aprenderia em cada um.
4. Vocês escolhem juntos o **MVP** (a menor versão que já resolve o problema).
5. Resultado: um arquivo `brain/plans/<nome>/brief.md` com tudo decidido.

**Dica:** peça uma segunda opinião ao Gemini:
```
agy --mode plan -p "Leia brain/plans/<nome>/brief.md e aponte riscos e alternativas"
```

## Etapa 2 — Alinhar (checar o brief)

Antes de seguir, confira se o brief responde a estas perguntas:

- [ ] Qual problema resolve, e para quem?
- [ ] O que **entra** no MVP e o que fica **para depois**?
- [ ] Qual a stack (ferramentas/linguagens) e por quê?
- [ ] Como vamos **provar que funciona** (testes, linter, checagem de tipos)?
- [ ] Como saberemos que deu certo (critério de sucesso)?

Se alguma resposta estiver vaga, volte para a Etapa 1. Resolver a dúvida agora é
barato; descobrir no meio do código é caro.

## Etapa 3 — Planejar

1. Diga: **"Planeje isso"** (ou `/plan`).
2. O agente quebra o trabalho em **fases pequenas**, cada uma entregável sozinha.
3. Resultado: arquivos em `brain/plans/<nome>/`.
4. Leia o plano. Se uma fase parecer grande demais, peça: "quebre a fase 2 em partes menores".

## Etapa 4 — Construir

### 4.1 Criar o repositório do projeto (só no primeiro dia)

Cada projeto tem o próprio repositório (o QG fica separado).
Peça ao Claude: **"Crie o repositório do projeto <nome> a partir do QG."**

Por baixo, ele vai:
```
gh repo create <nome> --private --clone
```
e copiar para lá o `AGENTS.md`, as skills e a estrutura do `brain/`.
*(Um script automático para isso está planejado: gap 7 do guia.)*

### 4.2 Registrar o trabalho (issue)

Cada fase do plano vira uma **issue** no GitHub:
```
gh issue create --title "Fase 1: ..." --body "Critérios de aceite: ..."
```

### 4.3 Executar — dois modos

**Modo acompanhado (recomendado enquanto você aprende):** você conversa com o Claude
e vê cada passo.

1. "Implemente a fase 1 do plano <nome>. Crie uma branch, explique cada decisão."
2. O Claude cria a branch (`feat/...`), escreve o código e roda os testes.
3. Pergunte à vontade: "por que você fez assim?", "o que esse comando faz?".

**Modo autônomo (Noodle):** os agentes trabalham sozinhos a partir do `todos.md`.

1. Adicione a tarefa em `todos.md`: `2. [ ] Fase 1 do plano <nome>`
2. Rode um ciclo de teste: `noodle start --once`
3. Acompanhe: `noodle status`
4. Quando estiver confiante: `noodle start` (loop contínuo).

> ⚠️ O Noodle ainda não foi testado de ponta a ponta neste PC (gap 5 do guia).
> Faça o primeiro `noodle start --once` junto com o Claude.

## Etapa 5 — Revisar

1. Peça ao agente: **"Revise isso"** (ou `/review`). Ele avalia arquitetura, qualidade,
   testes e desempenho, e dá notas de gravidade (alta/média/baixa).
2. O agente abre o **Pull Request**:
   ```
   gh pr create
   ```
3. **Você** revisa no GitHub:
   - Aba **Files changed**: verde entrou, vermelho saiu.
   - Comente numa linha clicando no **+**.
   - Tudo certo → **Squash and merge** → **Delete branch**.
4. Atualize seu PC:
   ```
   git switch main
   git pull
   ```

**Regra de ouro:** nada entra na `main` sem Pull Request.

## Etapa 6 — Publicar (produção)

Depende do tipo de projeto. Decidimos no brainstorm de cada um:

| Tipo | Onde publicar (sugestão) |
|---|---|
| Site / telas (Next.js) | Vercel (conecta ao GitHub e publica a cada merge) |
| Automação / script Python | Agendador do Windows, um servidor, ou GitHub Actions agendado |
| Chatbot | Depende do canal (web, WhatsApp, Telegram) — definir no brainstorm |

## Etapa 7 — Aprender (fechar o ciclo)

1. **"Reflita"** (ou `/reflect`): o agente salva no `brain/` o que aprendeu (erros,
   preferências, pegadinhas).
2. **"Crie um estudo de caso disso"**: vira um arquivo em `estudos/`.
3. Coloque o estudo de caso no **NotebookLM** e responda às perguntas da seção 7.
4. De vez em quando:
   - **"Medite"** (`/meditate`): faxina do `brain/`.
   - **"Rumine"** (`/ruminate`): garimpa conversas antigas atrás de padrões.

---

# Parte 2 — Comandos

## Claude Code (terminal: `claude`)

**Para abrir**

| Comando | O que faz |
|---|---|
| `claude` | Abre uma conversa nova na pasta atual |
| `claude -c` | Continua a última conversa |
| `claude --resume` | Escolhe uma conversa antiga para retomar |
| `claude -p "pergunta"` | Faz uma pergunta rápida e sai (não abre o chat) |
| `claude --model sonnet` | Abre já com um modelo específico |

**Dentro da conversa**

| Comando | O que faz |
|---|---|
| `/help` | Lista tudo o que dá para fazer |
| `/model` | Troca o modelo (`opus` pensa melhor, `sonnet` equilibrado, `haiku` barato) |
| `/clear` | Limpa a conversa (começa do zero, economiza tokens) |
| `/compact` | Resume a conversa para liberar espaço sem perder o fio |
| `/resume` | Volta para uma conversa anterior |
| `/memory` | Abre os arquivos de memória/regras |
| `/mcp` | Mostra as conexões externas (Notion, Google Drive...) |
| `/config` | Configurações |
| `/brainstorm`, `/plan`, `/review`, `/reflect` | Chama uma skill diretamente |

**Atalhos**

| Atalho | O que faz |
|---|---|
| `Esc` | Interrompe o agente no meio de uma ação |
| `Shift + Tab` | Alterna o modo (inclui o **modo plano**: o agente só planeja, não edita) |
| `@arquivo` | Aponta um arquivo específico na sua mensagem |
| `!comando` | Roda um comando do terminal sem sair do chat |

## Antigravity CLI (terminal: `agy`, Gemini)

| Comando | O que faz |
|---|---|
| `agy` | Abre a conversa |
| `agy -c` | Continua a última conversa |
| `agy -p "pergunta"` | Pergunta rápida e sai |
| `agy --mode plan` | Modo plano: analisa sem editar arquivos |
| `agy --model <nome>` | Escolhe o modelo |
| `agy models` | Lista os modelos disponíveis |
| `agy update` | Atualiza o programa |
| `?` (dentro do agy) | Mostra os atalhos |

## Noodle (orquestrador)

| Comando | O que faz |
|---|---|
| `noodle start --once` | Roda **um** ciclo (bom para testar) |
| `noodle start` | Liga o loop contínuo |
| `noodle status` | Mostra agentes ativos e fila |
| `noodle skills list` | Lista as skills que o Noodle enxerga |
| `noodle worktree list` | Lista as cópias de trabalho dos agentes |
| `noodle worktree prune` | Limpa worktrees já juntadas |
| `noodle reset` | Zera o estado interno (só com o loop parado) |

## Git (máquina do tempo)

| Comando | O que faz |
|---|---|
| `git status -sb` | Em que branch estou? Tem algo não salvo? |
| `git switch main` | Vai para a branch principal |
| `git switch -c feat/nome` | Cria uma branch nova e entra nela |
| `git pull` | Traz as novidades do GitHub |
| `git add arquivo` | Marca o arquivo para o próximo commit |
| `git commit -m "feat: mensagem"` | Salva um ponto na história |
| `git push` | Envia seus commits para o GitHub |
| `git log --oneline -10` | Mostra os últimos 10 commits |
| `git diff` | Mostra o que mudou e ainda não foi salvo |

**Padrão das mensagens de commit:**
`feat:` nova função · `fix:` correção · `docs:` documentação · `refactor:` reorganização
sem mudar comportamento · `test:` testes.

## GitHub CLI (`gh`)

| Comando | O que faz |
|---|---|
| `gh auth status` | Confere se você está logado |
| `gh repo create <nome> --private --clone` | Cria um repositório novo |
| `gh repo view --web` | Abre o repositório no navegador |
| `gh issue create` | Cria uma issue (tarefa) |
| `gh issue list` | Lista as issues abertas |
| `gh pr create` | Abre um Pull Request da branch atual |
| `gh pr list` | Lista os PRs abertos |
| `gh pr view --web` | Abre o PR no navegador |
| `gh pr merge --squash --delete-branch` | Faz o squash and merge pelo terminal |

---

# Parte 3 — Frases prontas

**Para começar**
- "Tenho uma ideia: ... Vamos fazer um brainstorm."
- "Quais são 3 jeitos diferentes de resolver isso, e o que eu aprenderia com cada um?"
- "Qual a menor versão disso que já seria útil?"

**Para aprender**
- "Explique o que você acabou de fazer como se eu fosse estudante."
- "O que esse comando faz, parte por parte?"
- "Por que você escolheu isso e não aquilo?"
- "Crie um estudo de caso disso para o NotebookLM."

**Para controlar a qualidade**
- "Antes de continuar, rode os testes e me mostre o resultado."
- "Revise isso como se fosse um engenheiro sênior exigente."
- "O que pode dar errado com essa solução?"

**Para a memória**
- "Anote essa correção no brain para não repetir."
- "Reflita sobre esta sessão."

**Para economizar**
- "Use um modelo mais barato para essa parte." / `/model haiku`
- `/clear` ao mudar de assunto: conversa longa = mais tokens a cada mensagem.

---

# Parte 4 — Quando algo dá errado

| Situação | O que fazer |
|---|---|
| "comando não reconhecido" logo após instalar algo | Feche e abra o terminal |
| O agente está indo pelo caminho errado | `Esc` e explique de novo |
| Fiz commit na `main` sem querer | Peça ao Claude: "fiz um commit na main por engano, crie uma branch com ele" |
| Conflito no merge | Peça ao Claude para explicar o conflito antes de resolver |
| O agente esqueceu algo combinado | "Leia o AGENTS.md e o brain/index.md de novo" |
| A conversa ficou lenta ou confusa | `/compact` ou `/clear` |
| Dúvida se o Gemini está com as regras | `agy --mode plan -p "Quais regras e skills você carregou?"` |

---

# Parte 5 — Regras de ouro

1. **Ideia nova → brainstorm primeiro.** Nunca direto para o código.
2. **Nada entra na `main` sem Pull Request.**
3. **Um agente por tarefa.** Dois agentes no mesmo arquivo = trabalho perdido.
4. **Prove que funciona.** Teste rodando > "acho que está certo".
5. **Tudo no GitHub.** Nunca mais projeto só no PC.
6. **Pergunte o porquê.** O objetivo é você aprender, não só receber pronto.
