# Caso 12 — Bot no Telegram, Resiliência de IA (Fallback OpenRouter) e Telemetria IoT

Data: 2026-10-04 · Repositório: `diaquinodev/meriadoque-bot` (local: `D:\PROJETOS\meriadoque-bot`)

---

## 1. Contexto

O usuário (estudante de Engenharia de IA) precisava acessar o seu agente Meriadoque de qualquer lugar pelo celular: consultar o backlog de tarefas (`todos.md`), tirar dúvidas técnicas consultando a memória do `brain/` e anotar ideias rapidamente sem estar na frente do computador.

No brainstorm inicial, a primeira ideia foi criar um bot de WhatsApp conectando via QR Code (emulando WhatsApp Web). O agente desafiou essa premissa apontando o risco real de banimento de número pela Meta, a fragilidade de sessões que caem a cada atualização do WhatsApp e a péssima formatação de código no aplicativo. Decidiu-se migrar para o **Telegram** (API oficial, gratuita e estável) rodando no próprio notebook do usuário como servidor local (*edge node*), com princípios de **UX/UI conversacional (CUI)** e **telemetria IoT**.

---

## 2. O que fizemos (em ordem)

| Fase | Entrega | Prova de Conclusão |
|---|---|---|
| 1 | Scaffold do projeto, `.venv` Python 3.12, `ruff`, `pytest`, `.env.example` e CI | `ruff check` limpo; `pytest` verde; CI configurado |
| 2 | Motor `OpenRouterRotator` com cadeia de fallback para modelos gratuitos | Testes com mock simulando HTTP 429 e transição de modelo |
| 3 | `QGKnowledgeReader` para parse do `todos.md` e busca no vault do `brain/` | Testes de leitura estruturada, inserção com `next-id` e busca |
| 4 | `DeviceMonitor` para telemetria de hardware (bateria, CPU, RAM, uptime) com `psutil` | Testes com mocks de sensores e detecção de bateria crítica |
| 5 | Design System conversacional em `formatters.py` com emojis semânticos | Testes unitários de formatação Markdown para Telegram |
| 6 | `SecurityFilter` com whitelist restrita ao `TELEGRAM_USER_ID` | Testes assíncronos bloqueando usuários invasores |
| 7 | Teclados persistentes, comandos e fluxo conversacional completo | Handlers com feedback imediato `typing`; `main.py` com polling |
| 8 | `BatteryWatcher` via `JobQueue` para alerta proativo de bateria fraca (< 20%) | Teste de transição de estado sem spam de mensagens |
| 9 | Documentação completa, `iniciar-bot.bat` e caso de estudo no QG | 25 testes passando; 9 PRs organizados em formato draft/ready |

Comandos que valem decorar:

```powershell
# No repositório do bot (D:\PROJETOS\meriadoque-bot)
.\.venv\Scripts\Activate.ps1
pytest -v tests/                             # Roda os 25 testes unitários
ruff check .                                 # Roda a verificação estática
ruff format --check .                        # Checa formatação de código
python main.py                               # Inicia o servidor do bot
```

---

## 3. Conceitos

- **CUI (Conversational User Interface):** Interface de usuário baseada em diálogo (chat). Ao contrário de uma tela cheia de botões e abas (GUI), a interação acontece por texto e comandos em linguagem natural.
- **Heurísticas de Nielsen em Bots:** Regras clássicas de usabilidade adaptadas para chat:
  - *Visibilidade do status:* Ação `typing` ("digitando...") instantânea para você saber que a IA está trabalhando e não travou.
  - *Reconhecimento em vez de recordação:* Botões fixos na tela (`[📋 Backlog]`, `[💻 Status]`) para você não ter que decorar comandos de cabeça.
- **Edge Node / Nó de Borda (IoT):** Em vez de pagar um servidor na nuvem (AWS/GCP), o seu próprio notebook em casa atua como um nó de processamento local na borda, acessando os discos físicos e respondendo à internet.
- **Fallback Chain (Cadeia de Contingência):** Analogia: se a linha telefônica principal (Gemini) estiver ocupada ou der sinal de ocupado (erro 429), o sistema disca automaticamente para a segunda linha (Llama), depois para a terceira (Qwen), até alguém atender.
- **Rate Limit (HTTP 429):** O limite de requisições que uma API gratuita aceita por minuto. Quando atinge o teto, o servidor devolve o código `429 Too Many Requests`.
- **Whitelist (Lista de Permissão):** Uma lista VIP. Se o seu ID numérico não estiver nela, a porta nem se abre.
- **Boundary Discipline:** Princípio de arquitetura onde a validação e conversão de dados são feitas exclusivamente na "borda" (onde o Telegram entrega a mensagem). Toda a lógica interna (`core/`) trabalha com dados puros e seguros.

---

## 4. Por que assim

- **Telegram em vez de WhatsApp não-oficial:** O WhatsApp bloqueia contas que usam bibliotecas não-oficiais (Baileys/WPPConnect) e a formatação de código é precária. O Telegram tem API oficial para bots, gratuita, sem risco de ban e com suporte primoroso a Markdown e botões.
- **OpenRouter com Fallback em vez de API direta:** O OpenRouter padroniza dezenas de provedores de IA sob uma única chave e biblioteca (`openai`). A lista de modelos `:free` permitiu custo zero absoluto com resiliência contra quedas de cotas.
- **Notebook local em vez de VPS:** Como o Meriadoque precisa ler o repositório local (`todos.md` e `brain/`), rodar no notebook permitiu acesso direto ao sistema de arquivos sem complexidade de sincronização com a nuvem.
- **PRs empilhados com Merge Commit (sem Squash):** Conforme documentado em `brain/codebase/github-pr-gotchas.md`, em projetos de fases progressivas o merge commit preserva os branches e evita conflitos em cadeia.

---

## 5. Hacks e pegadinhas

- **Ruff escaneando a `.venv` no Windows:** Se você rodar `ruff check .` sem configurar `exclude = [".venv"]` no `ruff.toml`, o linter tenta ler os 50.000 arquivos da biblioteca Python, congelando a execução.
- **Heredocs no PowerShell:** O delimitador `@' ... '@` do PowerShell exige que a tag de fechamento `'@` esteja colada na margem esquerda (coluna 0). Qualquer espaço causa travamento esperando mais linhas. A solução mais limpa foi escrever scripts Python auxiliares.
- **Fechar a tampa do notebook suspende o Windows:** Por padrão, o Windows desliga o Wi-Fi e suspende o processador quando você fecha a tela. É obrigatório ir em *Opções de Energia -> Escolher a função do fechamento da tampa* e marcar *Nada a fazer*.
- **`psutil.sensors_battery()` pode retornar `None`:** Em computadores desktop sem bateria ou em máquinas virtuais, essa função devolve `None`. O código precisa tratar isso defensivamente para não quebrar com `AttributeError`.

---

## 6. O que estudar (em ordem de prioridade)

1. **Python Asyncio e Bibliotecas de Bot:** Como o `python-telegram-bot` usa `async/await` para gerenciar mensagens concorrentes sem travar o programa.
2. **Resiliência e Padrões de Integração:** O padrão *Circuit Breaker* e *Fallback Strategy* para chamadas de APIs externas.
3. **Usabilidade Conversacional (CUI):** Artigos do Nielsen Norman Group sobre *Chatbot Usability* e feedback em interfaces de texto.
4. **Monitoramento com `psutil`:** Como coletar métricas de sistema operacional (CPU, memória, disco, bateria) para automações de infraestrutura.

---

## 7. Perguntas para o NotebookLM

Copie e cole estas perguntas no seu caderno do NotebookLM para testar seu entendimento:

1. *Qual é a principal diferença entre conectar um bot via WhatsApp não-oficial e via API oficial do Telegram, e quais são os riscos técnicos de cada abordagem?*
2. *Como funciona a cadeia de fallback implementada no OpenRouter e por que ela é indispensável ao utilizar modelos gratuitos?*
3. *Por que a Heurística de Visibilidade do Status é tão importante em chatbots de IA e como ela foi aplicada no Meriadoque Bot?*
4. *O que é o princípio de Boundary Discipline e como ele foi aplicado na separação entre as pastas `core/` e `telegram_ui/`?*
5. *Quais configurações específicas do Windows são necessárias para transformar um notebook doméstico em um servidor de automação que não dorme?*

---

## 8. Referências

- [Telegram Bot API Official Documentation](https://core.telegram.org/bots/api)
- [python-telegram-bot Documentation](https://docs.python-telegram-bot.org/)
- [OpenRouter API Reference & Models](https://openrouter.ai/docs)
- [Nielsen Norman Group — 10 Usability Heuristics for User Interface Design](https://www.nngroup.com/articles/ten-usability-heuristics/)
- [psutil Documentation (Cross-platform process and system utilities)](https://psutil.readthedocs.io/)
