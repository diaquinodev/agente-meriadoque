# Fase 5 — Conferência de prazo de repasse

Back to [[plans/auditor-de-repasses/overview]]

## Goal

Verificar se cada repasse caiu na data prevista pelo ciclo do contrato, considerando
fim de semana e feriados nacionais.

## Changes

- `src/auditor/settlement.py`: data esperada a partir da data da venda e do ciclo;
  rolagem para o próximo dia útil (lib `holidays`, BR); emite `PRAZO_VIOLADO`.
- Motor da fase 4 passa a chamar a checagem de prazo.

## Data structures

- Reusa `ContractRules.ciclo` e `Divergence`.

## Verification

- Runtime (pytest): venda no dia 12 → dia 25 do mesmo mês; venda no dia 20 → dia 10 do
  mês seguinte; dia de pagamento num domingo → segunda; num feriado nacional → próximo
  dia útil; pagamento 2 dias depois do esperado → divergência.

**Cut-line:** se atrasar, só fim de semana (sem feriados) e registrar como limitação.
