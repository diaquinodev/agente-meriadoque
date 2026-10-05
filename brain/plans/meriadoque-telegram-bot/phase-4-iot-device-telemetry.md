# Fase 4 — Telemetria de Hardware IoT

Back to [[meriadoque-telegram-bot/overview]]

## Objetivo

Implementar a coleta de métricas de telemetria de hardware no notebook servidor via biblioteca `psutil`, fornecendo informações de saúde do dispositivo (energia/bateria, carga de CPU, memória RAM e uptime).

## Mudanças

- `core/device_monitor.py`:
  - `get_device_telemetry() -> DeviceTelemetry`:
    - Bateria: porcentagem, se está conectado na tomada (`power_plugged`) e estimativa de tempo restante.
    - Recursos: porcentagem de uso de CPU (`psutil.cpu_percent`) e memória RAM (`psutil.virtual_memory`).
    - Uptime: tempo decorrido desde o boot do sistema ou início do bot.
  - `is_battery_critical(threshold: int = 20) -> tuple[bool, int]`:
    - Avalia se o notebook está rodando na bateria (desconectado da tomada) E a porcentagem está abaixo do limiar (default 20%).
- `tests/test_device_monitor.py`:
  - Testes com mocks do `psutil.sensors_battery`, `psutil.cpu_percent` e `psutil.virtual_memory`.
  - Validação do retorno de bateria crítica em cenários simulados (desplugado em 15% -> True; plugado em 15% -> False; desplugado em 80% -> False).

## Estrutura de Dados

```python
class DeviceTelemetry:
    battery_percent: int | None
    power_plugged: bool | None
    cpu_percent: float
    ram_used_gb: float
    ram_total_gb: float
    ram_percent: float
    uptime_human: str
```

## Verificação

### Estática
- `ruff check core/device_monitor.py tests/test_device_monitor.py`
- `pytest tests/test_device_monitor.py`

### Runtime
- Executar `python -c "from core.device_monitor import get_device_telemetry; print(get_device_telemetry())"` no notebook e conferir os valores reais do hardware.
