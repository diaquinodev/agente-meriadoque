# Manual do Agente Meriadoque

Passo a passo para usar o agente do começo ao fim de um projeto, e a lista de
comandos para você ganhar domínio das ferramentas.

> **Como ler este manual:** a Parte 0 mostra a **esteira real** do primeiro projeto.
> A Parte 1 é o **roteiro** (o que fazer, em que ordem). A Parte 2 são os **comandos**.
> A Parte 3 são **frases prontas** para pedir as coisas.
> Para entender os conceitos (o que é branch, skill, worktree...), veja `docs/GUIA-DO-AGENTE.md`.

---

# Parte 0 — A esteira na prática: o Auditor de Repasses

O primeiro projeto passou por todas as etapas deste manual. Use como modelo.

| Etapa | O que aconteceu | Tempo |
|---|---|---|
| Brainstorm | Ideia genérica → dor real do varejo (conciliação de taxas de marketplace) | ~1h |
| Segunda opinião | O `agy` (Gemini) revisou o brief e apontou 4 riscos, todos aceitos | 15 min |
| Plano | 10 fases pequenas, cada uma com prova de que funciona e "cut-line" | 30 min |
| Construção | Modo autônomo: o Claude construiu as 10 fases sozinho, 1 PR draft por fase | ~4h |
| Revisão | Você revisa e faz o merge dos 10 PRs, na ordem | com você |
| Publicação | README com eval, demo em vídeo, chave do Gemini, decidir se fica público | com você |
| Aprendizado | Estudos de caso 03 e 04 em `estudos/` | junto |

**Resultado:** repositório `auditor-de-repasses` com 66 testes, CI com eval em todo PR,
tela, relatório Excel e README. Detalhes em `estudos/caso-04-construindo-o-auditor.md`.

**Lições que viraram regra neste manual:**
- Uma dor real vence qualquer ideia genérica. Comece pela sua experiência.
- Peça sempre uma segunda opinião (outro modelo) sobre o brief antes do plano.
- Toda fase precisa de uma **prova** (teste, número, execução), não de "acho que funciona".
- IA escreve texto e julga; **código faz conta**.
- Menos PRs, maiores e com propósito: 1 por sessão no QG, 1 por fase nos projetos.

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
Traga a resposta para o Claude e peça: "avalie a análise do agy: o que aceitamos e o que
não?". No Auditor, essa revisão cortou chamadas de IA de 80 para 5.

**Dica:** material real da sua área (um teste de vaga, um relatório do trabalho) é ótimo
para descobrir **o que a área valoriza**. Use os conceitos, nunca o texto, a marca ou dados
reais num projeto público.

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

Um bom plano tem, **em cada fase**:
- **Objetivo** e arquivos afetados (poucos: 2 ou 3);
- **Prova de que funciona** (qual teste, qual número, qual comando);
- **Orçamento de horas**, se houver prazo;
- **Cut-line**: a versão mínima a entregar se o tempo apertar.

A ordem sempre começa pela **fundação** (repositório, testes, CI, tipos de dados) e só
depois vêm as funcionalidades.

## Etapa 4 — Construir

### 4.1 Criar o repositório do projeto (só no primeiro dia)

Cada projeto tem o próprio repositório (o QG fica separado), numa pasta própria:
`D:\PROJETOS\<nome>`. Peça ao Claude: **"Crie o repositório do projeto <nome>."**

O que acontece (foi assim no Auditor):
1. Pasta + ambiente virtual Python (`.venv`), que é uma "caixa" com as bibliotecas só
   daquele projeto.
2. `gh repo create <nome> --private`, com a `main` nascendo só com o README.
3. A **fase 1 é sempre a fundação**: `pyproject.toml`, linter (ruff), checagem de tipos
   (mypy), testes (pytest), CI no GitHub Actions e um `AGENTS.md` com as regras do projeto.

*(Um script de "novo projeto" que automatiza isso está planejado: gap 7 do guia.)*

### 4.2 Registrar o trabalho (issue)

Em projetos com mais pessoas, cada fase vira uma **issue**:
```
gh issue create --title "Fase 1: ..." --body "Critérios de aceite: ..."
```
Em projetos solo, o PR de cada fase aponta para o arquivo da fase no plano, o que já cumpre
esse papel.

### 4.3 Executar — três modos

**Modo acompanhado (recomendado enquanto você aprende):** você conversa com o Claude
e vê cada passo.

1. "Implemente a fase 1 do plano <nome>. Crie uma branch, explique cada decisão."
2. O Claude cria a branch (`feat/...`), escreve o código e roda os testes.
3. Pergunte à vontade: "por que você fez assim?", "o que esse comando faz?".

**Modo autônomo com Claude (validado no Auditor):** você aprova o plano e sai; o Claude
constrói tudo e deixa pronto para revisão.

1. Garanta que o plano está aprovado, com fases, provas e prazo.
2. Diga: **"Pode ir do começo ao fim sem interrupção; quando eu voltar, eu reviso."**
3. O Claude trabalha fase por fase: branch → código → testes → CI → **PR em draft**. Cada
   PR usa o anterior como base (**PRs empilhados**), para o trabalho não parar esperando
   merge.
4. Ele **não faz merge na `main`**: isso é seu. Ele também registra o que não conseguiu
   fazer (ex.: sem chave de API, sem navegador para prints).
5. Ao voltar: leia o relatório final dele e siga a Etapa 5.

**Modo autônomo com Noodle (experimental):** os agentes trabalham sozinhos a partir do `todos.md`.

1. Adicione a tarefa em `todos.md`: `2. [ ] Fase 1 do plano <nome>`
2. Rode um ciclo de teste: `noodle start --once`
3. Acompanhe: `noodle status`
4. Quando estiver confiante: `noodle start` (loop contínuo).

> ⚠️ O Noodle ainda não foi testado de ponta a ponta neste PC (gap 5 do guia).
> Faça o primeiro `noodle start --once` junto com o Claude.

## Etapa 5 — Revisar

**Regra de ouro:** nada entra na `main` sem Pull Request.

### 5.1 Quantos PRs? Depende do repositório

O revisor automático **CodeRabbit** (plano gratuito) tem cota por hora. Muitos PRs pequenos
esgotam a cota ("Review limit reached"), o que é só um aviso e não bloqueia o merge.

| Onde | Regra |
|---|---|
| **QG** (docs, brain, estudos) | 1 branch `sessao/AAAA-MM-DD`, 1 commit por assunto, **1 PR no fim da sessão**. Diga "fechamos a sessão" e o Claude abre o PR. |
| **Projetos de código** | 1 PR **por fase**, aberto como **draft** (rascunho). Quando o CI fica verde, você clica **Ready for review**, o CodeRabbit revisa uma vez e você faz o merge. |

### 5.2 Checklist de revisão de um PR

Antes de olhar, peça ao agente uma revisão crítica: **"Revise isso"** (ou `/review`).

- [ ] A descrição explica **o que** mudou e **por quê**?
- [ ] O CI está verde (✓ ao lado do commit)?
- [ ] Na aba **Files changed**, os arquivos alterados são os que a fase prometia?
- [ ] Existe teste provando o que a fase diz fazer?
- [ ] O CodeRabbit apontou algo? Pergunte ao Claude: "o CodeRabbit comentou X, faz sentido?"
- [ ] Você entendeu? Se não, pergunte antes do merge. Revisar é aprender.

### 5.3 Qual botão de merge usar

| Situação | Botão | Por quê |
|---|---|---|
| PR normal (base = `main`) | **Squash and merge** | Vira 1 commit limpo na `main` |
| **PRs empilhados** (cada um com base no anterior) | **Create a merge commit**, na ordem 1 → 2 → 3... | Mantém o histórico que o PR seguinte usa; com squash, cada PR seguinte daria conflito |

Depois de cada merge: **Delete branch**. O GitHub muda sozinho a base do próximo PR
empilhado para a `main`.

### 5.4 Depois do merge

```
git switch main
git pull
```
Ou peça ao Claude: "fiz o merge, sincroniza". Ele confere se o PR está mesmo `MERGED` antes
de apagar branches.

## Etapa 6 — Publicar (produção)

Depende do tipo de projeto. Decidimos no brainstorm de cada um:

| Tipo | Onde publicar (sugestão) |
|---|---|
| Site / telas (Next.js) | Vercel (conecta ao GitHub e publica a cada merge) |
| Automação / script Python | Agendador do Windows, um servidor, ou GitHub Actions agendado |
| Chatbot | Depende do canal (web, WhatsApp, Telegram) — definir no brainstorm |

### 6.1 Checklist de portfólio

- [ ] **README** com: problema, o que faz, diagrama, decisões, como rodar, resultados,
      **limitações** (honestidade conta pontos).
- [ ] **Demo** que roda sem chave de API (modo demo) e um **vídeo ou GIF** de 1–2 minutos.
- [ ] **Números**: resultado do eval, testes, CI verde.
- [ ] README testado **num clone limpo**, como um recrutador faria.
- [ ] Antes de tornar público: auditoria de e-mail pessoal no histórico, segredos, licenças
      de código de terceiros e dados reais. Peça ao Claude: "audite para tornar público".

### 6.2 Chaves de API (ex.: Gemini)

- Crie no site do provedor (Gemini: Google AI Studio → Get API key).
- **Nunca** cole a chave no chat nem em arquivo do repositório.
- Guarde numa variável de ambiente, só na sessão do terminal:
  ```
  $env:GEMINI_API_KEY = "sua-chave"
  ```
- Plano gratuito: use só dados fictícios (o provedor pode usar os dados) e conte com o limite
  de requisições (erro 429).

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
| `gh pr ready <número>` | Tira o PR do rascunho (Ready for review) |
| `gh pr checks <número>` | Mostra se o CI passou |
| `gh pr merge <número> --merge --delete-branch` | Merge commit (para PRs empilhados) |
| `gh run list` | Lista as execuções do CI |

## Projeto Python (dentro da pasta do projeto)

| Comando | O que faz |
|---|---|
| `python -m venv .venv` | Cria o ambiente virtual (só na primeira vez) |
| `.venv\Scripts\activate` | Ativa o ambiente (toda vez que abrir o terminal) |
| `pip install -e ".[dev]"` | Instala o projeto e as ferramentas de desenvolvimento |
| `pytest` | Roda os testes |
| `ruff check .` | Procura erros e padrões ruins no código |
| `ruff format .` | Formata o código no padrão |
| `mypy src` | Confere os tipos |
| `streamlit run app.py` | Abre a tela no navegador |
| `python -m auditor conciliar` | (Auditor) roda a conciliação e gera o Excel |
| `python -m auditor avaliar` | (Auditor) roda o eval |

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

**Para delegar e fechar ciclos**
- "O plano está aprovado. Pode ir do começo ao fim sem interrupção; quando eu voltar, eu reviso."
- "Fiz o merge, sincroniza."
- "Fechamos a sessão." (abre o PR único da sessão no QG)
- "Audite o repositório para tornar público."
- "Leia esta análise do agy e diga o que aceitamos e o que não."

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
| O agente esqueceu algo combinado | "Leia o `AGENTS.md` e o `brain/index.md` de novo" |
| A conversa ficou lenta ou confusa | `/compact` ou `/clear` |
| Dúvida se o Gemini está com as regras | `agy --mode plan -p "Quais regras e skills você carregou?"` |
| CodeRabbit: "Review limit reached" | É só a cota gratuita; não bloqueia o merge. Agrupe PRs (Etapa 5.1) |
| Conflito ao fazer merge de PRs empilhados | Use "Create a merge commit". Se já usou squash, peça ao Claude para rebasear o próximo PR |
| `streamlit`/`pytest` "não reconhecido" | Ative o ambiente: `.venv\Scripts\activate` |
| O Claude disse que terminou, mas você não viu funcionar | Peça: "me mostre a prova: rode e cole o resultado" |

---

# Parte 5 — Regras de ouro

1. **Ideia nova → brainstorm primeiro.** Nunca direto para o código.
2. **Nada entra na `main` sem Pull Request.**
3. **Um agente por tarefa.** Dois agentes no mesmo arquivo = trabalho perdido.
4. **Prove que funciona.** Teste rodando > "acho que está certo".
5. **Tudo no GitHub.** Nunca mais projeto só no PC.
6. **Pergunte o porquê.** O objetivo é você aprender, não só receber pronto.
7. **IA escreve texto; código faz conta.** Número importante nunca sai da IA.
8. **Segunda opinião antes do plano.** Um outro modelo revisa o brief.
