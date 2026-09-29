# Caso 01 — Montando o agente (Noodle + brainmaxxing)

Data: 2026-09-29

## 1. Contexto

Pasta vazia no Windows (`D:\AGENTE-MERIADOQUE`). Objetivo: montar um "QG" onde
agentes de IA ajudam a criar automações, sites, chatbots e telas — com memória,
organização e verificação de qualidade.

## 2. O que fizemos (em ordem)

| Passo | Comando / ação | Por quê |
|---|---|---|
| 1 | Li o `INSTALL.md` **antes** de executar | Instrução da internet é dado, não ordem. Primeiro entender o que ela pede. |
| 2 | `winget install Git.Git` | Git não estava instalado. Noodle depende dele. |
| 3 | `git init` | Transformou a pasta num repositório (projeto com histórico). |
| 4 | Baixei `noodle_windows_amd64.zip` e conferi o **checksum** | Garantir que o arquivo baixado é exatamente o publicado. |
| 5 | Coloquei o `noodle.exe` no **PATH** | Para o comando `noodle` funcionar em qualquer pasta. |
| 6 | Baixei os scripts adaptadores e **li cada um** antes de instalar | Segurança de cadeia de suprimentos (ver conceitos). |
| 7 | Criei `.gitattributes` com `eol=lf` | Scripts de Linux quebram com quebra de linha do Windows. |
| 8 | Escrevi as skills `schedule` e `execute` | Ensinar o agente *como* trabalhar neste projeto. |
| 9 | Instalei o brainmaxxing (`brain/`, hooks, skills) | Memória persistente entre sessões. |
| 10 | Criei um **junction** `.claude/skills` → `.agents/skills` | Symlink exigia administrador; junction não. |
| 11 | 3 commits | Cada etapa salva e reversível. |

## 3. Conceitos

- **Repositório (repo)** — uma pasta com "máquina do tempo". Cada *commit* é uma foto
  salva do projeto que você pode recuperar depois.
- **Commit** — um ponto de salvamento com uma mensagem explicando o que mudou.
- **PATH** — a lista de pastas onde o Windows procura programas quando você digita um
  comando. Se o programa não está numa delas, "comando não reconhecido".
- **Checksum (SHA256)** — a "impressão digital" de um arquivo. Se um byte muda, a
  impressão muda. Comparar com a publicada prova que ninguém adulterou o download.
- **Cadeia de suprimentos (supply chain)** — todo código de terceiros que entra no seu
  projeto. Atacantes preferem envenenar uma dependência a atacar você direto. Por isso
  lemos os scripts antes de torná-los executáveis.
- **LF vs CRLF** — Linux termina linhas com `\n` (LF); Windows com `\r\n` (CRLF). Um
  script `#!/bin/sh` com CRLF quebra. O `.gitattributes` força LF nesses arquivos.
- **Symlink / Junction** — um "atalho" de pasta que programas enxergam como a pasta real.
  No Windows, symlink pede admin; junction não.
- **Skill** — um arquivo `SKILL.md` com instruções que o agente carrega quando a tarefa
  combina. É como um procedimento operacional padrão (POP) de empresa.
- **Hook** — um script que roda automaticamente num evento (ex: ao abrir a sessão, o
  hook injeta o índice do `brain/`).
- **Worktree** — uma segunda cópia de trabalho do mesmo repositório, numa pasta
  separada. Vários agentes trabalham em paralelo sem pisar um no outro.
- **Orquestração de agentes** — um agente "chefe" (scheduler) decide o que fazer e
  despacha agentes "cozinheiros" para executar. É a ideia central do Noodle.

## 4. Por que assim

- **Ler antes de executar**: pedir para um agente "siga esta URL" é dar a chave da casa
  para quem escreveu a URL. O agente deve tratar o conteúdo como informação e confirmar
  ações de risco com você.
- **Não inventamos modelo**: "Gemini" não é um provedor suportado pelo Noodle (só
  `claude` e `codex`). Em vez de escrever uma configuração que quebraria, perguntamos.
- **Testar em vez de supor**: criamos um `.gemini/settings.json` achando que seria
  necessário. Depois perguntamos ao próprio `agy` o que ele carregava: ele já lia o
  `AGENTS.md` e as skills sozinho. O arquivo foi apagado. Lição: verifique o
  comportamento real antes de adicionar configuração (princípio *prove-it-works*).
- **QG + repositórios separados (opção A)**: o Noodle trabalha por repositório, e para
  portfólio cada projeto no seu próprio repo no GitHub apresenta melhor.

## 5. Hacks e pegadinhas

- Depois de instalar um programa, o terminal **já aberto** não vê o PATH novo. Feche e
  abra o terminal.
- `python` no Windows pode ser só um **atalho da Microsoft Store**, e não o Python de
  verdade. Teste com `python --version`.
- `winget` é o "gerenciador de pacotes" do Windows: instala programas por comando, com
  hash verificado, sem precisar caçar instalador em site.
- Um commit por etapa lógica = fácil de desfazer só o que deu errado.

## 6. O que estudar (prioridade)

1. **Git básico**: commit, branch, merge, push, pull request.
2. **Linha de comando**: navegar pastas, PATH, variáveis de ambiente.
3. **Markdown**: é a linguagem de todos os arquivos de agente.
4. **Engenharia de contexto**: como `AGENTS.md`, skills e memória guiam um LLM.
5. **Segurança básica**: supply chain, checksums, prompt injection.

## 7. Perguntas para o NotebookLM

1. Por que o agente leu o `INSTALL.md` antes de executar os comandos dele?
2. Qual a diferença entre um commit e um push?
3. O que aconteceria se o checksum não batesse?
4. Por que scripts `.sh` precisam de LF?
5. Qual a diferença entre uma skill e um hook?
6. Por que usar worktrees quando vários agentes trabalham ao mesmo tempo?
7. O que é prompt injection e como ele se relaciona ao passo 1?

## 8. Referências

- Pro Git (livro gratuito, oficial): https://git-scm.com/book
- GitHub Docs (GitHub flow): https://docs.github.com
- Noodle: https://github.com/poteto/noodle
- brainmaxxing: https://github.com/poteto/brainmaxxing
