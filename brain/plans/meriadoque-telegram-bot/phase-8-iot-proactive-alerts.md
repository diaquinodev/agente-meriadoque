# Fase 8 — Alertas Proativos de Bateria (IoT)

Back to [[meriadoque-telegram-bot/overview]]

## Objetivo

Implementar um job de segundo plano periódico utilizando a `JobQueue` do `python-telegram-bot`, monitorando o status de energia do notebook servidor a cada 5 minutos e enviando um alerta proativo no chat do usuário caso o cabo de energia tenha sido desconectado e a bateria esteja em nível crítico (< 20%).

## Mudanças

- `telegram_ui/jobs.py`:
  - `battery_check_job(context: ContextTypes.DEFAULT_TYPE)`:
    - Executa a cada 300 segundos (5 minutos).
    - Chama `is_battery_critical()` do `core/device_monitor.py`.
    - Mantém estado em memória para não repetir o alerta incessantemente a cada ciclo (dispara uma vez ao cruzar o limiar para baixo).
    - Se crítico: envia mensagem de alta prioridade para o `TELEGRAM_USER_ID`:
      `⚠️ *Alerta de Energia:* O notebook está desconectado da tomada e a bateria está em *{percent}%*. Conecte à energia para o Meriadoque não desligar!`
- `main.py`:
  - Registra o job na `application.job_queue`.
- `tests/test_battery_job.py`:
  - Teste unitário simulando a execução do job com estado de bateria normal vs bateria crítica, verificando se a mensagem é despachada apenas uma vez.

## Estrutura de Dados

```python
class BatteryAlertState:
    last_alerted_level: int | None
    alert_active: bool
```

## Verificação

### Estática
- `ruff check telegram_ui/jobs.py tests/test_battery_job.py`
- `pytest tests/test_battery_job.py`

### Runtime
- Desconectar propositalmente o notebook da tomada (ou alterar o limiar de teste para 95%) e aguardar a chegada da notificação no Telegram.
