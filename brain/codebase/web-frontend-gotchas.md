# Web frontend e testes de navegador — gotchas

Aprendido no dashboard de precificação (caso 08). Relaciona com [[principles/prove-it-works]].

- **Playwright sem baixar navegador:** `playwright-core` + `chromium.launch({ channel })`.
  O canal `msedge` procurou em `%LOCALAPPDATA%` e falhou, embora o Edge exista em
  `Program Files (x86)`. Tentar `chrome` primeiro e cair para `msedge`.
- **`node --test <pasta>` não funciona no Node 24** (tenta carregar a pasta como módulo).
  Usar glob entre aspas: `node --test "test/**/*.test.js"`.
- **Grid CSS + tabela larga = página rolando para o lado no celular.** A coluna implícita
  cresce até o `min-width` da tabela. Usar `grid-template-columns: minmax(0, 1fr)` no grid e
  `overflow-x: auto` só na caixa da tabela. Testar com
  `document.documentElement.scrollWidth - innerWidth <= 0` em 360 px.
- **Teste verde ≠ tela certa.** O e2e não viu o rótulo "Mais lucrativo" com lucro negativo nem
  o selo desalinhando cartões. Gerar print e **olhar** em claro/escuro e 3 larguras.
- **Ferramenta Write grava ` `/`﻿` como o caractere real** → ESLint
  `no-irregular-whitespace`. Em código, preferir `\s` (já cobre NBSP) e
  `String.fromCharCode(0xfeff)`; corrigir por número de linha com `sed`, não por padrão.
- **Prettier reformata depois do Write:** edições seguintes por texto exato falham. Reler o
  arquivo antes de editar.
- **Números exibidos vs motor:** o e2e compara o valor na tela com o motor importado no
  próprio teste — prova que a tela não altera o número.
- **Publicar:** antes de tornar um repo público, `git log -p --all | grep` por nome de cliente,
  links de planilha e chaves (o histórico inteiro, não só os arquivos). GitHub Pages no plano
  gratuito exige repo público; liga com
  `gh api -X POST repos/<dono>/<repo>/pages -f "source[branch]=main" -f "source[path]=/"`.
- **Electron renderer (sandbox) não resolve pacotes npm sem bundler:** Na janela do Electron com
  `contextIsolation: true` e `sandbox: true`, scripts rodando no navegador (`<script type="module">`)
  não conseguem importar pacotes como `import { z } from "zod"` diretamente via ES Modules
  (`Uncaught TypeError: Failed to resolve module specifier "zod"`). O `node --test` passa porque
  roda no Node, mas o navegador quebra na linha 0 e a tela congela silenciosamente em "carregando...".
  Módulos compartilhados entre main e renderer devem ser JavaScript puro sem dependências externas;
  validações de schema Zod devem ficar confinadas no processo `main` ou em arquivos exclusivos de backend.
