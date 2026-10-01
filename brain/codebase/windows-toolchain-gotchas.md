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
- **Arquivo travado por processo em execução (`EPERM: unlink`):** No Windows, se o app
  (ex: `Komorebi-Notes.exe` ou processo Electron em background/tray) estiver rodando,
  o `electron-packager` falha ao tentar sobrescrever binários (`.pak`, `.dll`, `.exe`).
  Correção: rodar `Stop-Process -Name "<Nome>" -Force` antes de iniciar nova build.
- **`electron-builder` com `--prepackaged`:** Gera o instalador NSIS (.exe) reaproveitando
  a pasta compilada pelo `electron-packager`, sem precisar recompilar ou baixar o runtime
  novamente do zero.
- **`fetch` nativo sem timeout trava por 240s no Windows:** No Node.js e Electron, chamadas
  `fetch` não possuem timeout padrão. Se a conexão com a nuvem congelar sem fechar o socket TCP,
  o Windows aguarda até o TCP keep-alive de 240 segundos (4 minutos). Sempre aplicar
  `AbortSignal.timeout(ms)` no cliente HTTP e uma corrida `Promise.race` na interface.
- **Scripts `.bat` precisam de feedback explícito:** Scripts de inicialização que leem `.env`
  ou variáveis de ambiente devem imprimir um checklist no console (`[OK] Chave detectada`).
  A ausência de log visual faz o usuário supor que o `.bat` não carregou as variáveis quando
  a lentidão for na verdade de rede externa.
- **Recarregamento no Electron (Main vs Renderer):** Recarregar a janela (F5 / Ctrl+R) só
  atualiza o processo Renderer (HTML/CSS/JS da tela). Alterações no processo Main (`main.js`,
  handlers IPC, backend) exigem fechar o processo Electron e iniciar novamente.

