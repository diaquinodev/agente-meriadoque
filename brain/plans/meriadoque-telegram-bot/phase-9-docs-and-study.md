# Fase 9 — Documentação e Estudo de Caso

Back to [[meriadoque-telegram-bot/overview]]

## Objetivo

Consolidar a documentação de uso do bot no `README.md` do novo repositório (com instruções passo a passo para criar o bot no `@BotFather`, obter o `user_id` e configurar o Windows para não suspender com a tampa fechada) e registrar o aprendizado no QG como `estudos/caso-12-bot-telegram-resiliencia-iot.md`.

## Mudanças

- `d:/meriadoque-bot/README.md`:
  - Visão geral da arquitetura e recursos (OpenRouter fallback, Design System CUI, Telemetria IoT).
  - Guia de configuração passo a passo:
    1. Criar bot no Telegram via `@BotFather` e copiar o token.
    2. Descobrir seu ID numérico via `@userinfobot`.
    3. Criar chave gratuita no `openrouter.ai`.
    4. Configurar `.env`.
    5. Configuração no Windows: Painel de Controle -> Opções de Energia -> *Escolher a função do fechamento da tampa* -> *Nada a fazer* (conectado à tomada).
    6. Execução via terminal ou como serviço em background.
- `d:/AGENTE-MERIADOQUE/estudos/caso-12-bot-telegram-resiliencia-iot.md`:
  - Estudo de caso didático para o NotebookLM abordando:
    - Heurísticas de Usabilidade (Nielsen) aplicadas a interfaces conversacionais (CUI).
    - Resiliência de IA com padrão Fallback Chain (lidando com HTTP 429).
    - O conceito de Edge Device / Nó IoT em computadores locais com telemetria via `psutil`.

## Verificação

### Estática
- Verificação de links markdown e integridade textual.
- `ruff check .` e suite completa de testes `pytest` rodando 100% verde no repositório do bot.

### Runtime
- Leitura do README do zero simulando a instalação e primeiro boot em ambiente limpo.
