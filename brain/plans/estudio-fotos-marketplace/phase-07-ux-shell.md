# Fase 7 — Protótipo e Shell Responsivo

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Validar a arquitetura visual antes de reconstruir os fluxos de produção.

## Changes
- Comparar protótipos de três estúdios, wizard adaptativo e workbench; registrar a escolha.
- Criar entrada `/studio` e shell responsivo com sidebar desktop e menu móvel.
- Definir navegação, feedback, estados vazios, carregamento e erro acessíveis.

## Data Structures
- `StudioModeCard`; `StudioRoute`; `NavigationState`.

## Verification
- Static: lint, tipos e teste de componentes de navegação.
- Runtime: screenshots desktop/mobile, teclado, foco, `aria-current`, drawer e contraste.
- Edge: texto ampliado, viewport estreita e sessão retomada.
