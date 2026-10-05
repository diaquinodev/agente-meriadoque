# Fase 1 — Scaffold e Ferramental

Back to [[meriadoque-telegram-bot/overview]]

## Objetivo

Criar a estrutura do novo repositório `d:/meriadoque-bot`, configurar ambiente virtual Python (`.venv`), dependências mínimas, regras de linting (`ruff`), framework de testes (`pytest`), template de variáveis de ambiente (`.env.example`) e workflow básico de CI.

## Mudanças

- Criar diretório do projeto `d:/meriadoque-bot` e inicializar Git com branch `main`.
- `pyproject.toml` ou `requirements.txt` + `requirements-dev.txt`:
  - `python-telegram-bot[job-queue]>=21.0`
  - `openai>=1.30.0`
  - `psutil>=5.9.0`
  - `python-dotenv>=1.0.0`
  - `ruff>=0.4.0`
  - `pytest>=8.0.0`
  - `pytest-asyncio>=0.23.0`
- `ruff.toml`: Configuração de linter e formatador alinhada aos projetos do QG.
- `.env.example`: Modelo de configuração com documentação clara para:
  - `TELEGRAM_BOT_TOKEN`
  - `TELEGRAM_USER_ID`
  - `OPENROUTER_API_KEY`
  - `MERIADOQUE_QG_PATH` (apontando para `d:/AGENTE-MERIADOQUE`)
- `.gitignore`: Ignorando `.env`, `.venv/`, `__pycache__/`, `.pytest_cache/`.
- `tests/test_scaffold.py`: Teste de fumaça inicial garantindo que o ambiente e imports básicos funcionam.

## Estrutura de Dados

- Configuração tipada ou dataclass simples para carregar variáveis de ambiente obrigatórias com validação antecipada (*fail-fast* se faltar alguma).

## Verificação

### Estática
- `ruff check .`
- `ruff format --check .`
- `pytest` executando `tests/test_scaffold.py` com sucesso.

### Runtime
- Ativar a `.venv` e executar `python -c "import telegram, openai, psutil; print('OK')"` retornando `OK`.
