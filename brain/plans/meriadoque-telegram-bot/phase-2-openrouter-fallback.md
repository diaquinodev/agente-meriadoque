# Fase 2 — Motor OpenRouter com Fallback

Back to [[meriadoque-telegram-bot/overview]]

## Objetivo

Implementar a classe `OpenRouterRotator` em `core/ai_rotator.py`, capaz de enviar prompts para o OpenRouter utilizando a biblioteca `openai`, iterando sobre uma lista de modelos gratuitos prioritários ao encontrar erros de Rate Limit (HTTP 429), timeouts ou instabilidade temporária.

## Mudanças

- `core/ai_rotator.py`:
  - Lista padrão de modelos gratuitos recomendados:
    1. `google/gemini-2.0-flash-exp:free`
    2. `meta-llama/llama-3.3-70b-instruct:free`
    3. `qwen/qwen-2.5-coder-32b-instruct:free`
    4. `deepseek/deepseek-r1:free`
  - Método `chat_completion(messages: list[dict], system_prompt: str | None = None) -> tuple[str, str]`:
    - Retorna a resposta de texto e o nome do modelo que conseguiu responder com sucesso.
    - Se um modelo falha com erro 429 ou erro de conexão, loga o aviso e passa silenciosamente para o próximo da cadeia.
    - Se todos os modelos da lista falharem, lança uma exceção customizada `AllModelsExhaustedError` com mensagem amigável.
  - Timeout explícito por chamada (ex: 30 segundos) para evitar que o bot fique preso em uma requisição lenta (conforme [[codebase/llm-output-guards]]).
- `tests/test_ai_rotator.py`:
  - Teste unitário com mock da API simulando resposta de sucesso no primeiro modelo.
  - Teste unitário simulando erro 429 no modelo 1 e recuperação bem-sucedida no modelo 2.
  - Teste simulando falha em todos os modelos e garantindo o lançamento de `AllModelsExhaustedError`.

## Estrutura de Dados

```python
class LLMResponse:
    content: str
    model_used: str
    attempts: int
```

## Verificação

### Estática
- `ruff check core/ai_rotator.py tests/test_ai_rotator.py`
- `pytest tests/test_ai_rotator.py`

### Runtime
- Teste real com a chave do OpenRouter enviando um "Olá" e conferindo qual modelo gratuito respondeu e o tempo de resposta.
