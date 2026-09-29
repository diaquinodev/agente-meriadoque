# Auditor de Repasses

Conciliação automática de taxas cobradas por payment gateways e marketplaces contra o
contrato, com agentes de IA onde há texto e julgamento, e código onde há conta.

- **Problem:** sellers de e-commerce recebem repasses com MDR, comissão, antecipação,
  cancelamentos e ajustes. Conferir linha a linha contra o contrato é manual, lento e
  sujeito a erro; cobranças indevidas viram perda silenciosa, e divergências favoráveis
  passam despercebidas até serem cobradas de volta.
- **Users:** analista financeiro de Fee Assurance / conciliação de um seller (varejo
  vendendo em site próprio via gateway e em marketplaces).
- **MVP scope (in):**
  - 1 parceiro fictício (payment gateway): MDR, antecipação por faixa de parcelamento
    (1x/Pix, 2x, 3x, 4x...), devolução de taxas no cancelamento, ciclo de pagamento.
  - Entrada: contrato em PDF (fictício) + arquivo de repasse em CSV.
  - Agente extrator: contrato → regras estruturadas (JSON validado por schema).
  - Motor de conciliação (código): valor esperado × cobrado por linha, data de
    pagamento esperada × real, divergências com sinal (contra e a favor).
  - Agente investigador: para divergências, hipóteses de causa (faixa errada,
    chargeback, estorno parcial, ajuste, aditivo) com nível de confiança.
  - Agente redator: rascunho de contestação com evidências (pedidos, valores, cláusula).
  - Aprovação humana antes de qualquer contestação.
  - Saídas: relatório Excel (linha a linha com OK/Divergente + resumo de impacto) e
    tela de revisão.
  - Eval: gerador de repasses fictícios com divergências plantadas; mede acertos,
    divergências não encontradas e alarmes falsos.
- **Out of scope (later):**
  - Múltiplos parceiros / marketplaces com formatos diferentes.
  - Aditivos contratuais versionados no tempo.
  - Módulo 2: reembolsos travados por nota fiscal perdida.
  - Envio real de e-mails / integração com portais dos marketplaces.
  - Interface em Next.js.
- **Chosen approach and why:** pipeline orquestrado com 3 agentes + motor determinístico.
  Cada agente existe por um motivo: o extrator lida com texto jurídico, o investigador
  com ambiguidade, o redator com comunicação. Toda conta fica em código porque LLMs
  erram aritmética e conciliação exige exatidão. Human-in-the-loop porque é dinheiro e
  relacionamento com parceiro.
- **Rejected alternatives and why:**
  - Um único agente fazendo tudo, inclusive as contas: impreciso, difícil de auditar.
  - Só planilha/fórmulas: resolve a conta, mas não lê contrato nem investiga causa.
  - Ruptura de estoque / triagem de suporte: IA ficaria decorativa ou o case seria comum.
- **Stack:** Python 3.12 (motor, agentes, eval, geração de dados); Streamlit para a tela
  do MVP (uma linguagem só, entrega mais rápida; Next.js fica para depois); LLM via API
  gratuita (Gemini, cota free) atrás de uma camada de abstração que permite trocar por
  Groq/Ollama; pandas + openpyxl para dados e Excel; pydantic para validar as regras
  extraídas.
- **Verification (lint/types/tests):** ruff (lint + formatação), mypy (tipos), pytest
  (motor de conciliação com casos conhecidos, incluindo os cenários clássicos: MDR,
  antecipação por faixa, cancelamento, ciclo de pagamento), eval com meta mínima de
  acerto antes de cada merge de fase. CI no GitHub Actions rodando tudo em cada PR.
- **Success criteria:**
  - Roda de ponta a ponta com um comando e com a tela: contrato + CSV → relatório Excel
    + contestações aprovadas.
  - Eval: ≥ 95% das divergências plantadas encontradas pelo motor; 0 falsos positivos
    em linhas corretas; investigador aponta a causa certa em ≥ 80% dos casos.
  - README com problema, diagrama, demo (vídeo curto/GIF), resultados do eval, custo e
    limitações.
- **Open questions:**
  - Prazo: há processo seletivo em andamento? (define ritmo e se vale Next.js)
  - Horas por semana disponíveis.
  - Ajustes de escopo que o usuário queira fazer no MVP.
  - Repositório público ou privado (depende da decisão sobre portfólio).
  - Todos os dados, nomes e contratos são fictícios; nenhum material de processo
    seletivo real é copiado para o projeto.
