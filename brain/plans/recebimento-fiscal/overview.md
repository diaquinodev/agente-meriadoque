# Recebimento Fiscal — plano

Brief: [[recebimento-fiscal/brief]]. Repo: `diaquinodev/recebimento-fiscal` (privado até a
fase final; público depois de auditoria). Pasta: `D:\PROJETOS\recebimento-fiscal`.

## Fases (1 PR draft por fase, empilhados, merge commit)

| # | Fase | Prova |
|---|---|---|
| 1 | Scaffold, CI, domínio (Pydantic), leitor NF-e XML, DV da chave, gerador sintético + gabarito | testes do XML e do DV; dados gerados conferidos |
| 2 | Banco (SQLAlchemy/SQLite), motor 3-way match com tolerâncias, eval | eval: 100% detecção, 0 falso bloqueio |
| 3 | SOAP: WSDL + endpoint de recebimento; SEFAZ simulado | teste envia envelope SOAP e recebe status |
| 4 | OCR/IDP: DANFE PDF fictícia, extração por visão, validação em código | chamada real lê DANFE de exemplo |
| 5 | Agente de exceções com function calling + aprovação humana | chamada real usa ≥2 ferramentas |
| 6 | Tela Streamlit, README, prints, publicação | e2e da tela (AppTest), CI verde |

## Status

- 2026-10-01 — brainstorm concluído; usuário delegou as decisões (SQLite hoje, Python,
  recorte acima).
- 2026-10-01 — fases 1–6 com PR (#1–#6) prontos, empilhados, em
  `diaquinodev/recebimento-fiscal` (privado). 72 testes; CI verde em #1–#5. Evals: motor
  480/480; OCR 22/22 campos fiscais (prompt v2); agente 12/12 (v3). Histórico auditado.
  Pendente: merge (usuário) e decisão de tornar público. Estudo: `estudos/caso-09`.
