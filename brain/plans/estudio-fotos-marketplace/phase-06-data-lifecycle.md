# Fase 6 — Sessões e Ciclo de Dados

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Persistir a linhagem completa e garantir isolamento, retenção e exclusão.

## Changes
- Criar sessões, peças, versões da ficha, identidades, cenas, assets e eventos de revisão.
- Isolar cache local por usuário e sessão; limpar dados no logout.
- Implementar exclusão reconciliada de banco, Storage e IndexedDB; definir retenção `[confirmar]` antes da execução.

## Data Structures
- `studio_sessions`; `garments`; `garment_spec_versions`; `session_garments`; `model_identity_versions`; `assets`; `human_review_events`; `deletion_jobs`.

## Verification
- Static: migrações e testes de persistência/RLS.
- Runtime: usuários A/B, logout/login, exclusão parcial simulada e retry idempotente.
- Edge: arquivo órfão, sessão interrompida, versão antiga e URL assinada expirada.
