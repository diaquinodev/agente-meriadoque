# Recebimento Fiscal Inteligente (Procure-to-Pay)

- Problem: empresa grande só pode pagar fornecedor depois do 3-way match (pedido de compra ×
  recebimento × NF-e). Divergência de preço, quantidade ou imposto bloqueia o pagamento e
  vira retrabalho entre compras, fiscal e fornecedor. A solução SAP NF-e GRC será
  descontinuada; o mercado paga por alternativas (AddTax, Qive, v360).
- Users: analista de recebimento fiscal / contas a pagar de empresa com ERP corporativo;
  recrutador de vagas de automação/IA em empresa grande.
- Origin: brainstorm 2026-10-01. Usuário quer portfólio para empresas grandes (não nicho de
  marketplace) e fechar gaps: function calling, OCR/IDP, SOAP/XML, Oracle, RAG, nuvem.
- MVP scope (in):
  - NF-e XML (layout 4.00, subconjunto: ide, emit, dest, det/prod, ICMS/IPI, total): leitura,
    validação de estrutura e do dígito verificador da chave.
  - Entrada por serviço SOAP (WSDL próprio) + SEFAZ simulado (consulta de situação por chave).
  - DANFE em PDF/imagem sem XML → extração por modelo de visão (OCR/IDP) + validação em código.
  - Motor de 3-way match item a item com tolerâncias (preço %, quantidade, imposto R$) →
    liberada / bloqueada (motivo) / rejeitada.
  - Agente de exceções com function calling: investiga, propõe ação e rascunho de e-mail;
    humano aprova.
  - Banco relacional (SQLAlchemy) com pedidos, recebimentos, fornecedores, tolerâncias.
  - Dados sintéticos com divergências plantadas + gabarito + eval.
  - Tela Streamlit (caixa de entrada, detalhe, aprovação) + README.
- Out of scope (later): RAG sobre política de compras e contratos (fase 2); Oracle real via
  Docker (fase 2; Docker não instalado em 2026-10-01); deploy em nuvem (fase 3); SEFAZ real
  (exige certificado digital da empresa); Databricks/Fabric (sem volume que justifique).
- Chosen approach and why: IA só onde há texto/imagem/julgamento (ler DANFE, explicar
  divergência, redigir); conta, tolerância e decisão de bloqueio em código determinístico
  ([[principles/boundary-discipline]]). Vocabulário de ERP corporativo (ME21N/MIGO/MIRO
  explicados), sem afirmar integração com SAP.
- Rejected alternatives and why: Conciliador de Devoluções de marketplace (nicho que o
  usuário não quer); assistente de contestação de taxas (não cobre OCR/SOAP); Databricks
  (encenação sem volume de dados).
- Stack: Python 3.12, FastAPI (API + SOAP), Pydantic v2, SQLAlchemy 2 (SQLite agora,
  Oracle depois), lxml, httpx, OpenRouter (Gemini com visão e tools; chave já configurada),
  fpdf2 (gerar DANFE fictícia), Streamlit.
- Verification (lint/types/tests): ruff, mypy strict, pytest; provedor de IA falso/gravado
  nos testes; eval do motor contra o gabarito no CI.
- Success criteria: 100% das divergências plantadas detectadas e 0 falso bloqueio no eval;
  chave com DV errado rejeitada; SOAP recebe XML e responde status; DANFE de exemplo lida
  com chamada real e conferida pelo código; agente resolve exceção usando ≥2 ferramentas
  numa chamada real; CI verde; README com prints.
- Open questions: Oracle local (Docker) ou Autonomous Free (cartão?) na fase 2.
