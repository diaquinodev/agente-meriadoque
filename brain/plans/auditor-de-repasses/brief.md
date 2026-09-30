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
  - Agente extrator: contrato → regras estruturadas (JSON validado por schema, via
    structured output).
  - **Gate humano 1:** tela mostra as regras extraídas; o usuário confirma ou corrige
    antes de conciliar (erro na extração contaminaria tudo).
  - Motor de conciliação (código): valor esperado × cobrado por linha, data de
    pagamento esperada × real (dias úteis + feriados nacionais), tolerância de
    arredondamento configurável (padrão R$ 0,02), divergências com sinal.
  - Agrupador (código): divergências agrupadas por "assinatura do erro" (tipo + regra +
    faixa), com impacto financeiro por grupo.
  - Agente analista: 1 chamada por grupo — hipótese de causa + cláusula violada +
    minuta de contestação com a lista de pedidos.
  - **Gate humano 2:** aprovação das contestações.
  - Modo demo: respostas de LLM gravadas; roda sem chave de API e sem rede.
  - Saídas: relatório Excel (linha a linha OK/Divergente + resumo por grupo) e tela.
  - Eval: gerador com 5 tipos de divergência plantada — taxa a maior (faixa trocada),
    taxa não devolvida no cancelamento, repasse fora do prazo, cobrança não prevista,
    taxa a menor (favorável); mede acertos, não encontradas e alarmes falsos.
- **Out of scope (later):**
  - Múltiplos parceiros / marketplaces com formatos diferentes.
  - Aditivos contratuais versionados no tempo.
  - Módulo 2: reembolsos travados por nota fiscal perdida.
  - Envio real de e-mails / integração com portais dos marketplaces.
  - Interface em Next.js.
- **Chosen approach and why:** pipeline orquestrado: extrator → gate humano → motor →
  agrupador → analista → gate humano. Revisado com o agy (Gemini): investigador e
  redator foram fundidos em um "analista" (menos latência, sem perda de contexto entre
  eles), e a análise passou a ser por lote de erro, não por linha — 1 a 3 chamadas de
  LLM por execução em vez de dezenas, o que cabe na cota gratuita e é como analistas
  contestam na prática. Toda conta fica em código porque LLMs erram aritmética.
  Orquestração aqui é coordenar etapas com papéis diferentes (IA, código, humano), não
  multiplicar agentes.
- **Rejected alternatives and why:**
  - Um único agente fazendo tudo, inclusive as contas: impreciso, difícil de auditar.
  - Só planilha/fórmulas: resolve a conta, mas não lê contrato nem investiga causa.
  - Ruptura de estoque / triagem de suporte: IA ficaria decorativa ou o case seria comum.
- **Stack:** Python 3.12 (motor, agentes, eval, geração de dados); Streamlit para a tela
  do MVP (uma linguagem só, entrega mais rápida; Next.js fica para depois); LLM via API
  gratuita (Gemini, cota free) atrás de uma camada de abstração que permite trocar por
  Groq/Ollama; pandas + openpyxl para dados e Excel; pydantic para validar as regras
  extraídas; `holidays` para feriados nacionais; `pypdf` para ler o contrato;
  `fpdf2` para gerar o contrato fictício em PDF.
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
- **Deadline:** 2026-09-30 (≈16h de trabalho, 8h/dia). Cortes já aplicados: Next.js,
  múltiplos parceiros, aditivos versionados, módulo de reembolso.
- **Open questions:**
  - Chave da API Gemini (Google AI Studio) — necessária só a partir da fase 6.
  - Repositório público ou privado na entrega (depende da decisão sobre portfólio).
  - Todos os dados, nomes e contratos são fictícios; nenhum material de processo
    seletivo real é copiado para o projeto.
