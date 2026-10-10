# Fase 9 — Motor Still

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Entregar o primeiro carro-chefe completo: Still frontal em manequim fantasma.

## Changes
- Criar fluxo independente com referência, ficha, fundo controlado, geração e revisão.
- Remover modelo, pose, acessórios e lifestyle desse contexto.
- Bloquear corpo, pele, cabide e partes visíveis de manequim.

## Data Structures
- `StillSession`; `StillSceneSpec`; `StillReview`.

## Verification
- Static: prompt e rota aceitam somente uma tomada frontal.
- Runtime: testar peça no chão, cabide e corpo em fundos neutros/texturizados.
- Visual: rubric de forma, cor, recortes, detalhes, volume e ausência humana.
