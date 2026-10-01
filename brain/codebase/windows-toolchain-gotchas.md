# Windows toolchain gotchas

Máquina do usuário: Windows 10, PowerShell 5.1 + Git Bash, IDE Antigravity (fork do VS Code).

- **npm bloqueia scripts de pós-instalação.** Pacotes que baixam binário no `postinstall`
  ficam quebrados em silêncio. Aconteceu com `@anthropic-ai/claude-code` e com `electron`.
  Correção: `node node_modules/<pacote>/install.js` (só para pacote confiável). Ver caso-05.
- **`ELECTRON_RUN_AS_NODE=1`** vem herdado do terminal do Antigravity/VS Code: o Electron
  abre como Node, sem janela. Rodar `Remove-Item Env:ELECTRON_RUN_AS_NODE` antes de
  `npm start`. Ver caso-05.
- **CRLF:** arquivo gravado em modo texto no Windows ganha `\r\n`. Para dados
  reprodutíveis, gravar com `newline="\n"` e fixar `eol=lf` no `.gitattributes`. Ver caso-04.
- **Gerar código-fonte via script Python (heredoc) corrompe escapes:** `\n` dentro de
  strings JS virou quebra de linha real; `\d` em regex gerou aviso. Escrever arquivos de
  código com a ferramenta Write/Edit, não por string Python.
- **`setx`** só vale para processos novos: o usuário precisa fechar e reabrir o
  Antigravity inteiro. Conferir chave só pela existência, nunca imprimir o valor.
- **Symlink exige admin:** `.claude/skills → .agents/skills` é uma junction,
  ignorada no git por ser específica da máquina.
- **Scripts `sh`** (adaptadores do Noodle, hooks) rodam via Git Bash.
- **Terminal Bash embaralha acentos** na saída; o arquivo gerado está correto. Conferir
  o arquivo, não a saída do terminal.
