# Fase 10 — Eval e README

Back to [[plans/auditor-de-repasses/overview]]

## Goal

Provar com números que o sistema funciona e contar a história para quem avalia.

## Changes

- `src/auditor/eval.py`: gera K cenários com sementes diferentes, roda o pipeline e
  compara com o gabarito — por tipo de divergência: encontradas, não encontradas, alarmes
  falsos; e acerto da causa do analista (modo demo ou Gemini). Saída em tabela + JSON.
- CI roda o eval em modo demo e falha abaixo das metas do brief.
- `README.md`: problema, diagrama do pipeline (mermaid), como rodar em 3 comandos,
  resultados do eval, decisões (IA × código, gates humanos, lote por assinatura),
  custo por execução, limitações, próximos passos. GIF/vídeo da demo gravado pelo
  usuário.

## Data structures

- `EvalReport` — métricas por tipo e globais.

## Verification

- Runtime: `python -m auditor.eval` → motor ≥ 95% de acerto e 0 falsos positivos;
  analista ≥ 80% de causa correta.
- Manual: seguir o README do zero numa pasta limpa (clone → instalar → rodar demo) e
  chegar ao Excel sem ajuda.

**Cut-line:** se atrasar, eval só do motor (determinístico) e acerto do analista medido
em uma rodada manual documentada no README.
