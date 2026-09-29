# Fase 4 — Motor de conciliação de taxas

Back to [[plans/auditor-de-repasses/overview]]

## Goal

Função pura: `ContractRules` + transações → divergências de valor. Zero IA.

## Changes

- `src/auditor/engine.py`: calcula MDR e antecipação esperados por linha (faixa por
  parcelas, Pix = 1x), valida devolução de taxas em cancelamentos, detecta tarifas não
  previstas, compara com tolerância configurável e emite `Divergence` com sinal.

## Data structures

- `ReconciliationResult` — linhas OK, divergências, totais esperado × cobrado.

## Verification

- Static: mypy, ruff.
- Runtime (pytest): um caso por regra — MDR correto; antecipação de cada faixa; faixa
  trocada (a maior e a menor); cancelamento com e sem devolução; tarifa extra; diferença
  de R$ 0,01 dentro da tolerância não vira divergência; R$ 0,03 vira.
- Integração: roda sobre `data/repasse.csv` e encontra todas as divergências de valor do
  gabarito.

**Cut-line:** nenhuma — é o coração do projeto.
