# Fase 7 — Camada de LLM e agente extrator

Back to [[plans/auditor-de-repasses/overview]]

## Goal

Ler o contrato PDF e produzir `ContractRules` validado, via LLM trocável e com modo demo.

## Changes

- `src/auditor/llm.py`: interface `LLMProvider` (gerar saída estruturada a partir de
  prompt + schema); `GeminiProvider` (structured output, retry com espera em erro 429);
  `ReplayProvider` (lê respostas gravadas em `data/llm_cache/`, modo demo). Escolha por
  variável de ambiente.
- `src/auditor/extractor.py`: PDF → texto (`pypdf`) → prompt → `ContractRules`
  (validação pydantic na fronteira; resposta inválida = erro claro, sem "consertar").
- Gravar a resposta real do Gemini em `data/llm_cache/` para o modo demo.

## Data structures

- `LLMProvider` (protocolo), `ExtractionResult` — regras + trechos de cláusula citados.

## Verification

- Static: mypy, ruff.
- Runtime: testes com `ReplayProvider` (sem rede) — contrato fictício gera exatamente as
  regras usadas pelo gerador da fase 3; JSON inválido do LLM gera erro legível.
- Manual (com chave): extração real com Gemini bate com as regras do gerador; resposta
  gravada no cache.
- Requer: chave da API Gemini (Google AI Studio) em variável de ambiente, nunca no git.
