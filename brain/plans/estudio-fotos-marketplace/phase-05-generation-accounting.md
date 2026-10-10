# Fase 5 — Geração e Créditos Atômicos

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Impedir cobrança duplicada, estorno indevido e corridas entre gerações.

## Changes
- Introduzir execução idempotente ligada à reserva de crédito.
- Tratar commit de reserva como obrigatório; falha não pode produzir entrega aprovada.
- Substituir expiração cega por lease/heartbeat de geração ativa.

## Data Structures
- `GenerationRun`; `IdempotencyKey`; `CreditReservationState`; `GenerationLease`.

## Verification
- Static: testes de crédito e migração passam em PostgreSQL.
- Runtime: repetir, concorrer, interromper e retomar a mesma requisição.
- Edge: falha antes do provedor, depois do provedor, no Storage e no commit de crédito.
