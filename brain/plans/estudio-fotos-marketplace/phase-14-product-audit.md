# Fase 14 — Auditoria Integral do Produto

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Provar o produto completo e procurar gargalos técnicos, lógicos e visuais antes de liberar.

## Changes
- Instalar Playwright, axe e regressão visual; instrumentar tempos e falhas por etapa.
- Executar o ledger de [[plans/estudio-fotos-marketplace/testing]] sem itens sem evidência.
- Corrigir somente causas raiz encontradas; registrar riscos residuais e custos reais.

## Data Structures
- `VerificationLedger`; `PerformanceSample`; `FidelityEvaluation`; `ResidualRisk`.

## Verification
- Static: lint, tipos, unitários, integração, banco e build limpos.
- Runtime: todos os fluxos em desktop/mobile, usuários distintos, retries e falhas injetadas.
- Visual/live: smoke tests do provedor, screenshots e avaliação humana cega das três sessões.
