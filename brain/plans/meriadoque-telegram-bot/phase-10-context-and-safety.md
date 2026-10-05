# Fase 10 — Memória Conversacional Multi-turn e Splitter de Mensagens Telegram

Back to [[meriadoque-telegram-bot/overview]]

## Objetivo

Eliminar dois gargalos críticos da experiência de uso do bot no Telegram:
1. **Perda de Contexto (Stateless):** Permitir conversas multi-turn onde o bot lembra as últimas mensagens trocadas, tornando as interações contínuas e contextuais.
2. **Estouro de Limite do Telegram (4096 caracteres) & Markdown Quebrado:** Garantir que respostas longas geradas por LLMs sejam divididas elegantemente em chunks de até 4000 caracteres, fechando blocos de código abertos para que a API do Telegram nunca rejeite a mensagem com erro 400.

## Mudanças

- `core/conversation_memory.py`:
  - Classe `ConversationMemory` gerenciando um buffer de histórico em memória por `chat_id`.
  - Janela deslizante configurável (padrão: 8 mensagens / 4 turnos).
  - TTL de inatividade (ex: 30 minutos sem mensagens reseta a conversa para evitar contaminação de assuntos antigos).
  - Método `format_messages_for_llm(chat_id, new_user_message, system_prompt)` pronto para injetar na API OpenRouter no formato `[{"role": "system", ...}, {"role": "user", ...}, {"role": "assistant", ...}]`.
- `core/ai_rotator.py`:
  - Atualização do método `generate_response` (ou novo método `chat_completion`) para aceitar a lista completa de mensagens `messages: list[dict[str, str]]`, mantendo a cadeia de fallback entre modelos gratuitos.
- `telegram_ui/safe_sender.py`:
  - Utilitário assíncrono `send_safe_message(update, context, text, parse_mode="Markdown")`:
    - Divide mensagens com mais de 4000 caracteres respeitando parágrafos (`\n\n`), linhas (`\n`) ou palavras.
    - Se houver bloco de código (```` ``` ````) aberto ao quebrar a mensagem, fecha o bloco na primeira parte e reabre na segunda para preservar a formatação de sintaxe.
    - Trata exceções de parse do Telegram (`telegram.error.BadRequest: Can't parse entities`) fazendo fallback automático para texto puro sem markdown.
- `telegram_ui/handlers.py`:
  - Integração do `ConversationMemory` e `send_safe_message` no `handle_message`.
  - Comando `/limpar` ou botão para resetar a memória conversacional manualmente quando o usuário quiser iniciar um novo tópico do zero.

## Verificação

### Estática
- `ruff check .` e `ruff format --check .` sem nenhum aviso.
- Tipagem estrita com anotações Python 3.12.

### Automatizada
- Testes unitários em `tests/test_conversation_memory.py`:
  - Teste de adição de mensagens e respeito ao tamanho máximo do buffer (FIFO).
  - Teste de expiração por TTL.
  - Teste de reset manual.
- Testes unitários em `tests/test_safe_sender.py`:
  - Teste de divisão de mensagem com mais de 4096 caracteres.
  - Teste de preservação de blocos de código ```` ``` ```` entre mensagens divididas.
  - Teste de fallback quando o markdown está quebrado.
- Suite completa `pytest` passando 100%.
