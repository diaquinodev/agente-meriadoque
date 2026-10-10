# Fase 4 — Motores de Prompt

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Eliminar contradições e dar a cada estúdio um contrato de prompt próprio.

## Changes
- Criar builders separados para Modelo, Still e Kits.
- Aplicar prioridade peça → escopo → identidade → complemento → cena → tomada.
- Normalizar a saída real para `1200 x 1200` e validar dimensões antes de anunciar o formato.

## Data Structures
- `ModelPromptContext`; `StillPromptContext`; `KitPromptContext`; `OutputImageMetadata`.

## Verification
- Static: testes semânticos comprovam regras obrigatórias e ausência de instruções proibidas.
- Runtime: gerar uma amostra por motor e inspecionar arquivo, dimensões e metadados.
- Edge: peça estampada, conjunto, material transparente e cenário em conflito com a cor.
