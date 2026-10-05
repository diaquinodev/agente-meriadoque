# Meriadoque Telegram Bot — Briefing

Interface remota segura e conversacional no Telegram para consultar a memória (`brain/`), o backlog (`todos.md`), interagir com a IA e monitorar o ambiente de trabalho remotamente.

- **Problem:** O usuário precisa acessar o agente Meriadoque, consultar seus projetos e registrar ideias de qualquer lugar pelo celular, sem precisar estar fisicamente na frente do computador ou abrir o editor de código.
- **Users:** O próprio usuário (estudante de Engenharia de IA), utilizando seu smartphone via Telegram.
- **MVP scope (in):**
  1. **Segurança (Whitelist):** O bot responde exclusivamente ao `TELEGRAM_USER_ID` do usuário; qualquer outra pessoa é sumariamente ignorada.
  2. **Motor de IA com Resiliência (OpenRouter Fallback Chain):** Integração com OpenRouter via SDK `openai`, com rotação automática entre modelos gratuitos de ponta (ex: Gemini 2.0 Flash, Llama 3.3 70B, Qwen 2.5 Coder, DeepSeek R1) ao detectar erros de Rate Limit (HTTP 429) ou timeout.
  3. **Consulta de Backlog e Memória:**
     - Leitura estruturada e resumo do `todos.md` (tarefas pendentes, em andamento e concluídas).
     - Leitura e busca de notas e princípios no `brain/` do QG (`d:/AGENTE-MERIADOQUE`).
  4. **Captura Rápida de Ideias:** Adicionar itens como rascunhos de ideias diretamente no `todos.md` pelo chat.
  5. **Design System Conversacional (CUI) & Heurísticas de Nielsen:**
     - *Visibilidade do status:* Ação `typing` imediata e indicação sutil no rodapé de qual modelo gerou a resposta (`[⚡ via Gemini 2.0 Flash]`).
     - *Reconhecimento em vez de recordação:* Menu inferior fixo com botões rápidos (`[📋 Backlog]`, `[🧠 Consultar Brain]`, `[💡 Nova Ideia]`, `[💻 Status do Notebook]`).
     - *Consistência visual:* Emojis semânticos padronizados (🧠, 📋, 💡, 💻, ⚠️, 🔄) e formatação Markdown limpa.
     - *Tratamento gracioso de erros:* Mensagens orientativas e transparentes quando a cota gratuita estiver temporariamente instável.
  6. **Consciência de Dispositivo (IoT / Edge Node):**
     - Telemetria do notebook servidor (`psutil`): status de energia/bateria, carga de CPU/RAM e tempo de atividade (*uptime*).
     - Alerta proativo caso o notebook seja desconectado da tomada e a bateria caia para nível crítico (< 20%).
- **Out of scope (later / Fase 2):**
  - Execução de comandos arbitrários de terminal shell via chat.
  - Criação automática de repositórios no GitHub ou disparo autônomo do loop do Noodle.
  - Envio e transcrição de mensagens de voz (áudio).
- **Rejected alternatives and why:**
  - *WhatsApp via QR Code (Baileys/WPPConnect):* Rejeitado devido ao risco de bloqueio de número pela Meta, instabilidade periódica de sessão no WhatsApp Web e suporte fraco a formatação de código. O Telegram oferece API oficial, gratuita, estável e livre de banimentos.
  - *API paga direta (Claude/OpenAI):* Rejeitado para o MVP em favor do OpenRouter com lista de fallback de modelos gratuitos, atendendo ao requisito de custo zero e alta resiliência.
  - *Execução de shell remoto no MVP:* Rejeitado por segurança e confiabilidade; o risco de travar processos ou disparar deleções acidentais sem visualização de tela não se justifica na primeira fase.
- **Stack:**
  - **Linguagem:** Python 3.11+
  - **Biblioteca Telegram:** `python-telegram-bot` (v20+ com suporte a `asyncio`)
  - **Cliente LLM:** `openai` (configurado para `https://openrouter.ai/api/v1`)
  - **Telemetria de Sistema:** `psutil`
  - **Configuração e Ambiente:** `python-dotenv`
- **Verification (lint/types/tests):**
  - **Linter e Formatador:** `ruff check .` e `ruff format --check .`
  - **Testes Unitários:** `pytest` cobrindo a cadeia de fallback do OpenRouter, a leitura segura do `todos.md` e o parse das notas do `brain/`.
- **Success criteria:**
  - Enviar uma mensagem no Telegram pelo celular e receber a resposta contextualizada em menos de 5 segundos.
  - Conseguir consultar o backlog (`todos.md`) e receber um resumo organizado em lista com botões.
  - Inserir uma ideia pelo celular e vê-la gravada corretamente no `todos.md`.
  - Simular queda de um modelo no OpenRouter e verificar a transição automática para o próximo modelo da lista sem que o usuário perceba erro.
  - Checar a telemetria do notebook (`/device`) e receber status de bateria e consumo de recursos.
- **Open questions:**
  - O nome final do bot no Telegram (ex: `@MeriadoqueQGBot`).
  - Caminho do repositório no disco (sugerido: `d:/meriadoque-bot`).
