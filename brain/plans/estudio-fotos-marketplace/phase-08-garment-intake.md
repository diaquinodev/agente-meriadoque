# Fase 8 — Entrada e Ficha da Peça

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Transformar fotos imperfeitas em uma ficha confirmada sem expor JSON ao seller.

## Changes
- Aceitar frente obrigatória e costas/detalhes opcionais no chão, cabide ou corpo.
- Executar preflight de nitidez, cobertura, obstrução e tipo de referência.
- Mostrar atributos e confiança; seller confirma ou corrige antes de congelar a versão.

## Data Structures
- `ReferencePreflight`; `GarmentExtraction`; `ConfidenceLevel`; `ConfirmedGarmentVersion`.

## Verification
- Static: testes de validação e transições da ficha.
- Runtime: enviar, trocar, remover, reordenar, corrigir e confirmar pelo teclado e toque.
- Edge: sem costas, foto com rosto, baixa resolução, conjunto e múltiplas peças acidentais.
