# Fase 1 — Provedor e Linha de Base

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Remover o modelo hardcoded descontinuado e provar que uma geração com referência funciona antes do redesign.

## Changes
- Centralizar modelo e capacidades em configuração validada no servidor.
- Migrar a rota para a OpenRouter Image API com `openai/gpt-image-2.5-sunburst`, preservando uma configuração explícita e validada por modelo para troca controlada.
- Registrar versão do provedor, custo da imagem e falhas sem expor segredo ou imagem do usuário.
- Manter autenticação, reserva de créditos e persistência ativas em qualquer geração real; modo simulado exige `MOCK_GENERATION=true` e nunca chama o provedor.

## Data Structures
- `ImageProviderConfig`; `ProviderGenerationResult`; `ProviderFailure`.

## Verification
- Static: lint, tipos, testes da rota e build.
- Runtime: smoke test pela rota com referência frontal; confirmar resposta de imagem, modelo usado, erro legível, contrato de parâmetros suportados e nenhuma cobrança duplicada.
- Edge: chave ausente, modelo inválido, timeout e resposta sem imagem.

## Status
- A versão inicial com Gemini foi integrada no PR https://github.com/diaquinodev/estudio-fotos-marketplace/pull/1; a decisão de OpenRouter exige um corretivo isolado antes da Fase 2.
- Evidência em 2026-10-10: um smoke direto da OpenRouter com referência frontal retornou imagem, mas não exerceu a configuração padrão da rota e, portanto, não conclui a fase.
- Pendente para concluir a fase: corrigir contrato de parâmetros, retirar bypass de autenticação, tornar retries idempotentes e executar lint, tipos, testes, build e smoke pela rota.
