# Fase 8 — Agente analista (investiga + redige por grupo)

Back to [[plans/auditor-de-repasses/overview]]

## Goal

Para cada `DivergenceGroup`: hipótese de causa, cláusula violada e minuta de contestação
— uma chamada de LLM por grupo.

## Changes

- `src/auditor/analyst.py`: prompt com contrato (trechos relevantes) + grupo (números já
  calculados pelo motor); saída estruturada. Os valores monetários da minuta vêm do
  motor, não do LLM — o texto só referencia os números.
- Grupos favoráveis geram "comunicado de divergência a favor", não contestação.
- Respostas gravadas no cache para o modo demo.

## Data structures

- `GroupAnalysis` — causa provável, confiança (baixa/média/alta), cláusula, minuta,
  ação recomendada (contestar / informar / investigar).

## Verification

- Runtime (ReplayProvider): cada grupo da demo recebe análise; minuta contém os pedidos e
  o impacto exatos do grupo (checagem automática contra o motor); grupo favorável não
  vira contestação.
- Manual (com chave): análises reais coerentes para os 5 tipos; nº de chamadas =
  nº de grupos.
