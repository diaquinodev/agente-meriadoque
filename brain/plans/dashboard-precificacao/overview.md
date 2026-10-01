# Dashboard de Precificação — plano

Brief: [[dashboard-precificacao/brief]]. Repo: `diaquinodev/dashboard-precificacao` (público).
Pasta local nova: `D:\PROJETOS\dashboard-precificacao` (a original com ç fica intocada).

## Fases (1 PR draft por fase, empilhados, merge commit)

| # | Fase | Prova |
|---|---|---|
| 1 | Scaffold + motor (taxas, solver por faixa, catálogo/CSV) + testes | `npm run check` verde; regressão dos 5 bugs |
| 2 | Tela: design tokens, layout responsivo, catálogo (upload/link/manual/exemplo), cartões, tabela | `npm run e2e` no Edge em 360/768/1280 px + prints |
| 3 | Docs (README, planilha, taxas, arquitetura) + GitHub Pages + tornar público | demo abre pelo link; auditoria sem dado de cliente |

## Bugs do original (regressão obrigatória)

1. Taxa = 0 volta ao padrão (`x || padrão`).
2. TikTok perto de R$ 79: prejuízo de R$ 2 com margem 0%.
3. CSV `59.90` → 5990.
4. Margem impossível → preço absurdo sem aviso.
5. Preço manual `1.234,56` → 1,234.

## Status

- 2026-10-01 — brief e plano escritos; usuário aprovou anonimizar, público, corrigir antes.
