# Fase 9 — Relatório Excel e tela

Back to [[plans/auditor-de-repasses/overview]]

## Goal

Tornar o pipeline usável por um analista financeiro: Excel no formato que a área usa e
uma tela com os dois gates humanos.

## Changes

- `src/auditor/report.py`: Excel com abas — Resumo (impacto por grupo, totais), Linhas
  (cada transação com esperado × cobrado e status OK/Divergente), Contestações aprovadas.
- `src/auditor/pipeline.py`: orquestra extrator → motor → agrupador → analista (funções
  puras + providers injetados), usado pela tela e pelo eval.
- `app.py` (Streamlit): 1) upload do contrato → tabela de regras editável →
  **Confirmar regras**; 2) upload do CSV → divergências e grupos; 3) análises por grupo →
  **Aprovar / Rejeitar** cada minuta; 4) baixar Excel. Seletor Demo / Gemini.

## Data structures

- `PipelineRun` — regras confirmadas, resultado, grupos, análises, aprovações.

## Verification

- Runtime (pytest): Excel gerado abre com openpyxl, tem as 3 abas e os totais batem com
  o motor.
- Manual: `streamlit run app.py` em modo demo, fluxo completo; print de cada etapa.
  Editar uma taxa no gate 1 muda o resultado (prova que o gate funciona).

**Cut-line:** se atrasar, a tela mostra as regras em tabela editável simples e as
minutas com um único botão "Aprovar todas".
