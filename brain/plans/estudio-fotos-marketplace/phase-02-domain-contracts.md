# Fase 2 — Contratos do Produto

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Definir os dados corretos antes de reescrever lógica ou telas.

## Changes
- Criar contratos separados para Modelo, Still e Kits.
- Substituir JSON livre, tecido manual e `SHOT_TYPES` compartilhado por planos tipados.
- Definir pacotes 1/3/5/10, Still frontal e três composições de Kit.

## Data Structures
- `GarmentTruthV1`; `GarmentField<T>`; `ReferenceAsset`; `ModelIdentityLock`; `SceneSpec`; `SupportingLookSpec`; `ShotPlan`.

## Verification
- Static: testes unitários validam IDs, ordem e quantidade de tomadas.
- Runtime: imprimir planos de todos os modos e confirmar que nenhuma tomada incompatível aparece.
- Edge: conjunto, cropped, bottom, ausência de costas e Kit incompleto.
