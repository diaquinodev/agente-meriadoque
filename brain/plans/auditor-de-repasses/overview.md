# Auditor de Repasses — Plano

Brief: [[plans/auditor-de-repasses/brief]]

## Status (2026-09-29)

- Fases 1–10 implementadas; 10 PRs draft empilhados em `diaquinodev/auditor-de-repasses`
  (#1 base `main`, #N base fase N-1), CI verde em todos. Código local em
  `D:\PROJETOS\auditor-de-repasses`.
- Desvios do plano: pandas removido (csv + openpyxl bastam); `ReplayProvider` complementado
  por `OfflineProvider` (demo sem IA), pois não havia chave do Gemini.
- Pendente com o usuário: revisar e fazer merge (1 → 10, "Create a merge commit"); criar a
  chave do Gemini e rodar `avaliar --provedor gemini --cenarios 3` + `conciliar --gravar`;
  prints da tela; decidir se o repositório fica público.

## Context

Seller de e-commerce precisa conferir se as taxas e prazos de repasse de um payment
gateway batem com o contrato. Hoje é manual; cobranças indevidas viram perda silenciosa.
Case de portfólio: orquestração de agentes com IA só onde há texto/julgamento, código
determinístico onde há conta, dois gates humanos e eval mensurável.

## Scope

**In:** 1 gateway fictício; regras de MDR, antecipação por faixa de parcelamento,
devolução de taxas no cancelamento, ciclo de pagamento em dias úteis; contrato PDF +
repasse CSV; extrator (LLM) → gate humano → motor → agrupador → analista (LLM) → gate
humano; relatório Excel; tela Streamlit; modo demo sem rede; eval com 5 tipos de
divergência plantada; README com resultados.

**Out:** Next.js, múltiplos parceiros/formatos, aditivos versionados, reembolso de NF,
envio real de e-mail, deploy em nuvem (demo local + vídeo/GIF).

## Constraints

- Prazo 2026-09-30, ~16h de trabalho. Cada fase tem orçamento de horas; **cut-lines**
  marcadas nas fases 4, 9 e 10.
- LLM só via API gratuita (Gemini) e sempre atrás de `LLMProvider`; modo demo obrigatório.
- Dados 100% fictícios. Nenhum material de processo seletivo real no repositório.
- Windows + Python 3.12. Repositório próprio: `diaquinodev/auditor-de-repasses`.
- Git: 1 branch + 1 PR **draft** por fase; *Ready for review* só com CI verde.

**Alternativas avaliadas (arquitetura dos agentes):**

| Opção | Resumo | Decisão |
|---|---|---|
| A. Lote por assinatura de erro | Motor agrupa divergências; 1 chamada de LLM por grupo | ✅ Escolhida: poucas chamadas (cota gratuita), realista, auditável |
| B. Linha a linha | Investigador + redator por divergência | ❌ Estoura cota, gera dezenas de minutas repetidas |
| C. Tudo em código | Causas inferidas por regras fixas | ❌ Não lida com cláusulas e exceções em texto |

Fronteiras (boundary-discipline): PDF/CSV/LLM são fronteiras — validar com pydantic
ali; motor, agrupador e relatório são funções puras sobre tipos já validados.

## Applicable skills

- `execute` (implementação por fase em worktree/branch)
- `review` (antes de marcar cada PR como pronto)
- `reflect` + caso de estudo em `estudos/` ao final

## Phases

| # | Fase | Orçamento |
|---|---|---|
| 1 | [[plans/auditor-de-repasses/phase-01-scaffold]] | 1h |
| 2 | [[plans/auditor-de-repasses/phase-02-domain-types]] | 1h |
| 3 | [[plans/auditor-de-repasses/phase-03-synthetic-data]] | 2h |
| 4 | [[plans/auditor-de-repasses/phase-04-fee-engine]] | 1,5h |
| 5 | [[plans/auditor-de-repasses/phase-05-settlement-dates]] | 1h |
| 6 | [[plans/auditor-de-repasses/phase-06-grouping]] | 1h |
| 7 | [[plans/auditor-de-repasses/phase-07-llm-extractor]] | 2h |
| 8 | [[plans/auditor-de-repasses/phase-08-analyst]] | 1,5h |
| 9 | [[plans/auditor-de-repasses/phase-09-report-and-ui]] | 2,5h |
| 10 | [[plans/auditor-de-repasses/phase-10-eval-and-readme]] | 2h |

Total ≈ 15,5h. Fases 1–6 não dependem de LLM (dia 1); 7–10 no dia 2.

## Verification (projeto)

- `ruff check .` e `ruff format --check .`
- `mypy src`
- `pytest`
- `python -m auditor.eval` → métricas acima das metas do brief
- `streamlit run app.py` em modo demo: fluxo completo contrato → regras → CSV →
  divergências → contestações → Excel baixado e aberto
- CI (GitHub Actions) rodando lint + tipos + testes + eval em cada PR
