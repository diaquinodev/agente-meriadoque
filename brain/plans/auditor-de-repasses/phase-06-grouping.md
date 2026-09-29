# Fase 6 — Agrupamento por assinatura do erro

Back to [[plans/auditor-de-repasses/overview]]

## Goal

Transformar dezenas de divergências em poucos grupos ("todas as vendas 4x cobradas com a
taxa de 3x"), com impacto financeiro por grupo. É o que reduz as chamadas de LLM.

## Changes

- `src/auditor/grouping.py`: agrupa por (tipo, regra, faixa/campo, taxa cobrada);
  soma impacto; ordena por impacto absoluto.

## Data structures

- `DivergenceGroup` — assinatura, tipo, regra/cláusula, pedidos, quantidade, impacto
  total (com sinal), exemplo representativo.

## Verification

- Runtime (pytest): 30 linhas com o mesmo erro → 1 grupo com 30 pedidos; erros
  diferentes → grupos diferentes; soma dos impactos dos grupos = soma das divergências;
  grupo favorável mantém sinal positivo para o seller.
- Integração: sobre `data/repasse.csv` produz entre 3 e 8 grupos.
