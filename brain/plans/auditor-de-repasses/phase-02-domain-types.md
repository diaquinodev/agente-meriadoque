# Fase 2 — Tipos de domínio

Back to [[plans/auditor-de-repasses/overview]]

## Goal

Definir as estruturas de dados que todas as fases usam. Mudar isso depois é reescrita;
agora é uma linha.

## Changes

- `src/auditor/models.py`: modelos pydantic.

## Data structures

- `ContractRules` — MDR (%), faixas de antecipação por nº de parcelas/Pix, regra de
  devolução no cancelamento, ciclo de pagamento (janelas de dias → dia de pagamento),
  regra de dia útil, lista de tarifas permitidas, referência de cláusula por regra.
- `Transaction` — pedido, tipo (venda/cancelamento), meio (cartão/Pix), parcelas, bruto,
  MDR cobrado, antecipação cobrada, outras tarifas, líquido, data da venda, data do
  pagamento.
- `DivergenceType` — enum: `TAXA_A_MAIOR`, `TAXA_A_MENOR`, `TAXA_NAO_DEVOLVIDA`,
  `PRAZO_VIOLADO`, `COBRANCA_NAO_PREVISTA`.
- `Divergence` — pedido, tipo, campo, esperado, cobrado, diferença (com sinal), regra e
  cláusula de origem.
- Valores monetários em `Decimal` (nunca float).

## Verification

- Static: mypy strict, ruff.
- Runtime: testes de validação — percentuais fora de 0–100 rejeitados, parcelas ≤ 0
  rejeitadas, `Decimal` preservado em ida e volta JSON.
