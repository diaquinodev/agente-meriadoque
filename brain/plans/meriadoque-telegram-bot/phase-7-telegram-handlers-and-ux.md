# Fase 7 — Handlers e UX do Telegram

Back to [[meriadoque-telegram-bot/overview]]

## Objetivo

Implementar a interface de usuário conversacional no Telegram: teclados persistentes com botões de acesso rápido (`telegram_ui/keyboards.py`), roteamento de comandos e mensagens de texto livre (`telegram_ui/handlers.py`), com feedback imediato de digitação (*typing*).

## Mudanças

- `telegram_ui/keyboards.py`:
  - `get_main_keyboard() -> ReplyKeyboardMarkup`:
    - Layout de 2 colunas:
      - `[📋 Backlog]` | `[🧠 Consultar Brain]`
      - `[💡 Nova Ideia]` | `[💻 Status do Notebook]`
- `telegram_ui/handlers.py`:
  - `start_command`: Exibe boas-vindas com a apresentação do Meriadoque e ativa o teclado fixo na tela.
  - `help_command`: Guia rápido de uso.
  - `backlog_handler`: Lê o `todos.md` usando o leitor da Fase 3 e formata com a Fase 5.
  - `device_handler`: Coleta a telemetria da Fase 4 e formata com a Fase 5.
  - `new_idea_handler`: Inicia o fluxo de captura de ideia (pergunta o texto ou aceita `/anotar <ideia>`) e grava no `todos.md`.
  - `brain_search_handler`: Busca no vault do `brain/` e exibe os arquivos relevantes.
  - `chat_message_handler`: Mensagens de texto livre enviadas pelo usuário:
    - Aciona `send_chat_action(ChatAction.TYPING)`.
    - Constrói o contexto com instruções do Meriadoque + princípios do `brain/`.
    - Chama o `OpenRouterRotator` da Fase 2.
    - Responde no Telegram com o texto formatado.
- `main.py`:
  - Configura o `ApplicationBuilder`, registra os handlers e inicia o polling do bot.

## Verificação

### Estática
- `ruff check telegram_ui/ tests/`
- `pytest tests/`

### Runtime
- Iniciar o bot (`python main.py`), abrir o Telegram no smartphone, enviar `/start`, clicar no botão `[📋 Backlog]`, clicar no botão `[💻 Status do Notebook]` e enviar uma pergunta técnica, observando o feedback `digitando...` e a resposta da IA.
