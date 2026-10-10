# Fase 13 — Revisão, Galeria e Exclusão

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Tornar aprovação humana real e fechar o ciclo até exportação ou exclusão.

## Changes
- Estados `processing`, `needs_review`, `approved`, `rejected` e `failed`.
- Comparação lado a lado, zoom, motivo de rejeição, retoque, retry e histórico.
- Liberar download individual/lote apenas para aprovadas; filtrar e excluir por sessão.

## Data Structures
- `AssetReviewState`; `HumanReviewEvent`; `ExportManifest`; `DeletionStatus`.

## Verification
- Static: reducer/máquina de estados e permissões de exportação.
- Runtime: testar todos os botões, teclado, falhas, refresh e retomada.
- Edge: persistência falha, URL expira, exclusão parcial e download misto.
