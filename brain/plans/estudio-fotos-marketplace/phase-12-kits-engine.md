# Fase 12 — Motor Kits

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Criar capas de Kit com até três itens fiéis dentro de uma única composição.

## Changes
- Upload e manifesto obrigatório para cada item/cor.
- Composições corpo inteiro, close e sobreposição, sempre em `1200 x 1200` real.
- Regeneração preserva regras, ordem e identidade de todos os itens.

## Data Structures
- `KitManifest`; `KitItem`; `KitComposition`; `KitValidationResult`.

## Verification
- Static: validar mínimo/máximo, IDs, cores e composição.
- Runtime: adicionar, remover, reordenar, nomear e regenerar.
- Visual: contagem exata, nenhuma duplicação/ausência e detalhes não misturados.
