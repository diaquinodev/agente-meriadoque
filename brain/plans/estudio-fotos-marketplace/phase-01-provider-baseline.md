# Fase 1 — Provedor e Linha de Base

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Remover o modelo hardcoded descontinuado e provar que uma geração com referência funciona antes do redesign.

## Changes
- Centralizar modelo e capacidades em configuração validada no servidor.
- Migrar a rota para `gemini-3.1-flash-image`, preservando configuração explícita para troca controlada.
- Registrar versão do provedor e falhas sem expor segredo ou imagem do usuário.

## Data Structures
- `ImageProviderConfig`; `ProviderGenerationResult`; `ProviderFailure`.

## Verification
- Static: lint, tipos, testes da rota e build.
- Runtime: smoke test com referência frontal; confirmar resposta de imagem, modelo usado, erro legível e nenhuma cobrança duplicada.
- Edge: chave ausente, modelo inválido, timeout e resposta sem imagem.

## Status
- Implementação publicada no PR draft https://github.com/diaquinodev/estudio-fotos-marketplace/pull/1.
- Evidência estática em 2026-10-10: 53 testes passaram; typecheck, lint e build passaram. Os 11 testes de banco foram pulados sem `TEST_DATABASE_URL`.
- Pendente para concluir a fase: smoke real com chave Gemini e referência frontal; o ambiente da sessão não tinha `GEMINI_API_KEY` nem imagem adequada.
