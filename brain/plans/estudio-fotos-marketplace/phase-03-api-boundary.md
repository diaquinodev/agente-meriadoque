# Fase 3 — Fronteira da API

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Fazer a API aceitar somente requisições completas, coerentes e identificáveis.

## Changes
- Validar payload, enums, tamanhos, modo, tomada, referências e limite agregado.
- Rotular cada imagem como frente, costas, detalhe, identidade ou item de Kit.
- Rejeitar costas sem referência, Kits incompletos e combinações incompatíveis.

## Data Structures
- `ControlledGenerationRequest`; `LabeledReference`; `GenerationValidationError`.

## Verification
- Static: testes de rota para cada sucesso e rejeição.
- Runtime: enviar requisições válidas e adulteradas contra servidor local.
- Edge: MIME falso, base64 inválido, payload grande, referência duplicada e modo desconhecido.
