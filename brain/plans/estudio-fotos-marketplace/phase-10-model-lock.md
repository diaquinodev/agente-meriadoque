# Fase 10 — Modelo e Foto Teste

Back to [[plans/estudio-fotos-marketplace/overview]]

## Goal
- Criar e bloquear uma identidade de modelo antes de gerar o ensaio.

## Changes
- Configuração progressiva de pele, idade aparente, corpo, tamanho, cabelo e styling.
- Incluir opções regulares, mid-size e plus-size sem presumir numeração universal.
- Aprovar/rejeitar foto teste; a aprovada vira referência obrigatória e congela a identidade.

## Data Structures
- `ModelIdentityDraft`; `ModelIdentityVersion`; `TestShotDecision`; `LockedSessionConfig`.

## Verification
- Static: testes impedem pacote sem teste aprovado e alteração após lock.
- Runtime: gerar, rejeitar, regenerar, aprovar e tentar modificar campos travados.
- Visual: comparar rosto, corpo, pele, cabelo e styling entre teste e amostras seguintes.
