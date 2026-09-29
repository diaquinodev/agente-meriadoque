# Fase 3 — Dados fictícios e gabarito

Back to [[plans/auditor-de-repasses/overview]]

## Goal

Gerar o contrato fictício (PDF) e repasses fictícios (CSV) com divergências plantadas e
um gabarito. Base da demo, dos testes e do eval.

## Changes

- `src/auditor/synthetic.py`: gera N transações corretas a partir de `ContractRules` e
  injeta divergências dos 5 tipos (incluindo lotes com o mesmo erro, como na vida real).
  Semente aleatória fixa → resultado reproduzível.
- `src/auditor/contract_pdf.py`: renderiza o contrato fictício (gateway inventado,
  cláusulas numeradas) em PDF.
- `data/`: contrato PDF, `repasse.csv`, `gabarito.json` versionados para a demo.

## Data structures

- `GroundTruth` — lista de (pedido, `DivergenceType`) plantados.

## Verification

- Static: mypy, ruff.
- Runtime: mesma semente → arquivos idênticos (idempotência); CSV sem divergência
  plantada tem 100% das linhas coerentes com as regras; PDF abre e o texto extraído
  contém as taxas.
