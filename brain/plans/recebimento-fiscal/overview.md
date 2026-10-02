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
- 2026-10-02 — histórico auditado (sem chaves, `.env` ou arquivo grande) e repo **público**.
  Fase 7 (PR #7, base `fase-6-tela`): banco por visitante, `requirements.txt` para o
  Streamlit Community Cloud, README com "Teste online". 73 testes. Pendente (usuário):
  merge #1→#7 e deploy em share.streamlit.io; conferir o link do README.
- 2026-10-02 — **tudo na `main`** (#1 e #6 por squash; #2/#4 fechados, conteúdo no #6).
  `main` idêntica ao testado; CI da `main` verde. Pendente: Streamlit apontar para `main`,
  apagar `fase-2-match`, `fase-4-ocr` e depois `fase-7-publicacao`; testar o link público.
- 2026-10-02 — **no ar:** https://recebimento-fiscal.streamlit.app (público, sem login).
  Testado: 24 notas (6/12/6), parecer da nfe-002 com 5 ferramentas, aprovação registrada;
  2ª aba começa vazia (banco por visitante). `fase-2-match`/`fase-4-ocr` apagadas.
  Para automatizar: a tela fica num iframe; abrir `/~/+/` para clicar.
