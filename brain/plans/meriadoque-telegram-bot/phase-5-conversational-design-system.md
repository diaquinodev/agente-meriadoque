# Fase 5 — Design System Conversacional

Back to [[meriadoque-telegram-bot/overview]]

## Objetivo

Implementar a camada de formatação visual e textual em `core/formatters.py`, aplicando o Design System conversacional e as Heurísticas de Usabilidade de Nielsen (consistência de ícones, hierarquia clara em Markdown, feedback legível).

## Mudanças

- `core/formatters.py`:
  - `format_todos_message(todos_data: dict) -> str`:
    - Gera mensagem Markdown com listas limpas e organizadas por status:
      - 📥 **Inbox / Ideias**
      - ⏳ **Pendentes**
      - 🚀 **Em Andamento**
      - ✅ **Concluídas**
  - `format_device_message(telemetry: DeviceTelemetry) -> str`:
    - Formatação da telemetria IoT com ícones de status:
      - 🔋 Bateria: `85% (Conectado à tomada ⚡)` ou `18% (Desconectado ⚠️)`
      - 🖥️ CPU: `12.4%`
      - 🧠 Memória RAM: `7.2 GB / 16.0 GB (45%)`
      - ⏱️ Uptime: `4h 32m`
  - `format_ai_response(content: str, model_used: str) -> str`:
    - Adiciona a assinatura sutil no rodapé da mensagem:
      `\n\n—\n⚡ *via {model_used}*`
  - `format_error_message(user_friendly_text: str) -> str`:
    - Formata avisos com o ícone semântico `⚠️` e recomendações claras de ação.
- `tests/test_formatters.py`:
  - Testes unitários validando cada função de formatação com dados representativos.
  - Garantia de escape de caracteres especiais do Markdown V2 do Telegram para evitar falhas de renderização.

## Estrutura de Dados

- Funções puras: recebem dicionários ou dataclasses e retornam strings formatadas prontas para envio.

## Verificação

### Estática
- `ruff check core/formatters.py tests/test_formatters.py`
- `pytest tests/test_formatters.py`

### Runtime
- Teste de renderização gerando strings e imprimindo no terminal para checar a estética e legibilidade.
