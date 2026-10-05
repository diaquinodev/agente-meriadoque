# Meriadoque Telegram Bot — Plano de Implementação

Brief: [[meriadoque-telegram-bot/brief]]
Repositório: `diaquinodev/meriadoque-bot` (local: `D:\PROJETOS\meriadoque-bot`)

## Contexto

Interface remota conversacional no Telegram para consultar a memória do agente (`brain/`), o backlog (`todos.md`), tirar dúvidas técnicas via IA e monitorar a saúde do notebook servidor através de telemetria IoT.

## Arquitetura

O projeto adota o princípio de **Boundary Discipline** ([[principles/boundary-discipline]]):
- **Camada Central (`core/`):** Lógica pura, sem dependência da biblioteca do Telegram.
  - `ai_rotator.py`: Cliente OpenRouter com cadeia de fallback automático para modelos gratuitos.
  - `brain_reader.py`: Leitura e busca segura no `brain/` e leitura/escrita no `todos.md`.
  - `device_monitor.py`: Coleta de telemetria de hardware (bateria, CPU, RAM, uptime) com `psutil`.
  - `formatters.py`: Design system conversacional, transformando dados em Markdown enriquecido.
- **Camada de Interface (`telegram_ui/`):** Shell fino acoplado ao `python-telegram-bot`.
  - `middleware.py`: Whitelist restrita ao `TELEGRAM_USER_ID`.
  - `keyboards.py`: Teclados persistentes e botões inline rápidos.
  - `handlers.py`: Roteamento de comandos e mensagens, com feedback imediato (`typing`).
  - `jobs.py`: Verificações periódicas em background (alerta proativo de bateria fraca).
- **Ponto de Entrada (`main.py`):** Inicialização do bot, validação de variáveis de ambiente (`.env`) e amarração das camadas.

## Fases de Implementação (1 PR draft por fase)

| # | Fase | Arquivo do Plano | Prova de Conclusão |
|---|---|---|---|
| 1 | Scaffold e Ferramental | [[meriadoque-telegram-bot/phase-1-scaffold]] | Repositório criado; `ruff` e `pytest` verdes no CI |
| 2 | Motor OpenRouter com Fallback | [[meriadoque-telegram-bot/phase-2-openrouter-fallback]] | Testes com mock simulando erro 429 e transição de modelo |
| 3 | Leitor de Memória e Backlog | [[meriadoque-telegram-bot/phase-3-brain-todos-reader]] | Testes de parse do `todos.md` e busca no `brain/` |
| 4 | Telemetria de Hardware IoT | [[meriadoque-telegram-bot/phase-4-iot-device-telemetry]] | Testes de medição de CPU, RAM e checagem de bateria |
| 5 | Design System Conversacional | [[meriadoque-telegram-bot/phase-5-conversational-design-system]] | Testes de formatação Markdown e emojis semânticos |
| 6 | Whitelist e Segurança | [[meriadoque-telegram-bot/phase-6-telegram-security-whitelist]] | Testes de bloqueio de IDs não autorizados |
| 7 | Handlers e UX do Telegram | [[meriadoque-telegram-bot/phase-7-telegram-handlers-and-ux]] | Bot responde no Telegram com botões e typing ativo |
| 8 | Alertas Proativos de Bateria | [[meriadoque-telegram-bot/phase-8-iot-proactive-alerts]] | Job periódico envia alerta simulado de bateria < 20% |
| 9 | Documentação e Estudo de Caso | [[meriadoque-telegram-bot/phase-9-docs-and-study]] | README completo + `estudos/caso-12-bot-telegram-resiliencia-iot.md` |
| 10 | Memória Multi-turn e Splitter Telegram | [[meriadoque-telegram-bot/phase-10-context-and-safety]] | Buffer de contexto + divisão elegante de mensagens >4096 caracteres |

## Verificação Geral

- **Estática:** `ruff check .` e `ruff format --check .`
- **Automatizada:** `pytest --cov=core --cov=telegram_ui tests/`
- **Ao Vivo:** Envio de mensagem pelo smartphone no Telegram com notebook na bateria, testando consulta ao backlog e telemetria.

## Status

- 2026-10-04 — Fases 1 a 9 concluídas! Repositório `diaquinodev/meriadoque-bot` criado em `D:\PROJETOS\meriadoque-bot`.
  - PRs #1 a #9 abertos no GitHub com 25 testes unitários passando (`pytest`).
  - Verificação estática e formatação 100% limpas com `ruff`.
  - Estudo de caso didático criado em `estudos/caso-12-bot-telegram-resiliencia-iot.md`.
  - Pendente: usuário adicionar suas chaves no `.env` e realizar os merges no GitHub.
