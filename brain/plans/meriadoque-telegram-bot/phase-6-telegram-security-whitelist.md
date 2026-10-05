# Fase 6 — Whitelist e Segurança no Telegram

Back to [[meriadoque-telegram-bot/overview]]

## Objetivo

Implementar a camada de segurança no Telegram em `telegram_ui/middleware.py`, garantindo que apenas o usuário com o `TELEGRAM_USER_ID` autorizado possa interagir com o bot, prevenindo abusos, consumo indevido de cotas e acesso a arquivos locais.

## Mudanças

- `telegram_ui/middleware.py`:
  - Decorator ou filtro customizado `restricted_to_owner(func)`:
    - Compara `update.effective_user.id` com o `authorized_user_id` carregado das configurações.
    - Se autorizado: executa a função normalmente.
    - Se não autorizado: loga um aviso de segurança no console (`[WARN] Tentativa de acesso bloqueada do User ID: 123456`) e não responde à mensagem (ou responde uma mensagem genérica de erro estático).
- `tests/test_security_middleware.py`:
  - Teste unitário passando `update` com ID correto e conferindo que o handler é chamado.
  - Teste unitário passando `update` com ID incorreto e conferindo que o handler é abortado e bloqueado.

## Estrutura de Dados

```python
class SecurityFilter:
    authorized_id: int
    def is_authorized(user_id: int) -> bool
```

## Verificação

### Estática
- `ruff check telegram_ui/middleware.py tests/test_security_middleware.py`
- `pytest tests/test_security_middleware.py`

### Runtime
- Teste com mock de evento do Telegram verificando a negação de acesso para IDs divergentes.
