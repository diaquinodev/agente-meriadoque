# Caso 04 — Construindo o Auditor de Repasses em 10 fases

Data: 2026-09-29 · Repositório: `diaquinodev/auditor-de-repasses` (privado)

## 1. Contexto

Com o plano de 10 fases pronto e prazo de um dia, o agente construiu o projeto inteiro de
forma autônoma enquanto o usuário estava fora, deixando tudo para revisão depois.

## 2. O que fizemos (em ordem)

| Fase | Entrega | Prova de que funciona |
|---|---|---|
| 1 | Repositório, ruff, mypy, pytest, CI | CI verde no GitHub |
| 2 | Tipos de domínio (pydantic) | 8 testes de validação |
| 3 | Dados fictícios + contrato PDF + gabarito | mesma semente = mesmos arquivos |
| 4 | Motor de conciliação de taxas | acha exatamente as divergências plantadas |
| 5 | Prazo com dias úteis e feriados | 12/10 feriado + sábado → 13/10 |
| 6 | Agrupamento por assinatura do erro | 80 divergências → 5 grupos |
| 7 | Camada de LLM + agente extrator + modo demo | extração do PDF = regras originais |
| 8 | Agente analista + checagem anti-alucinação | pega valor inventado pelo modelo |
| 9 | Excel, pipeline, CLI e tela Streamlit | fluxo da tela simulado em teste |
| 10 | Eval + README | 20 cenários, 100% achadas, 0 falsas |

Total: 66 testes, CI com eval em todo PR, 10 PRs empilhados.

## 3. Conceitos

- **PR empilhado (stacked PR)** — cada PR tem como base o PR anterior, não a `main`. Permite
  revisar uma fase por vez sem travar o trabalho esperando merge.
- **Decimal vs float** — `float` guarda números em binário e não representa 0,1 exato; em
  dinheiro isso vira centavos errados. `Decimal` guarda em base 10.
- **pydantic** — biblioteca que valida dados na entrada: se o CSV ou a IA mandar algo fora do
  formato, vira erro claro em vez de bug escondido.
- **Fronteira (boundary)** — lugar onde dados de fora entram no sistema (arquivo, IA). Só ali
  se valida; o miolo confia nos tipos.
- **Retry com backoff exponencial** — ao bater no limite da API (erro 429), esperar 2s, 4s,
  8s... antes de tentar de novo, em vez de insistir e piorar.
- **Replay** — gravar respostas reais da IA e reproduzi-las depois, sem rede e sem custo.
- **Placeholder** — marcador (`{{impacto}}`) que a IA escreve no lugar do número; o código
  substitui pelo valor exato. Evita alucinação de valores.
- **Eval** — prova automática com gabarito. Aqui: recall (quantas achou das que existiam) e
  falsos positivos (quantas apontou sem existir).
- **Teste de tela sem navegador** — `streamlit.testing.AppTest` clica nos botões da tela em
  código e confere o resultado.
- **Clone limpo** — testar o README numa pasta nova, como um recrutador faria.

## 4. Por que assim

- **Fases pequenas com prova em cada uma**: um erro aparece na fase em que nasceu, não no fim.
- **Teste que falhou por erro do teste**: o teste esperava 80 linhas divergentes, mas eram 70
  (algumas transações têm dois problemas). A correção foi calcular o esperado a partir do motor,
  e não "ajustar o número" para passar.
- **Modo demo honesto**: sem chave do Gemini, criamos um provedor sem IA para a demo e o CI
  rodarem. O README diz claramente que o 100% do eval em modo demo mede o código, não a IA.
- **Parar o que você mesmo ligou**: o servidor de teste da tela ficou exposto na rede; foi
  desligado logo depois da verificação.

## 5. Hacks e pegadinhas

- No Windows, arquivos gravados em modo texto ganham `\r\n` (CRLF). Para dados reprodutíveis,
  grave com `newline=""` e force LF no `.gitattributes`.
- `ruff` com `select` amplo pega muita coisa cedo (imports, genéricos antigos, linhas longas).
- mypy estrito obriga a pensar no tipo de cada valor, o que evitou erros no relatório Excel.
- Merge de PRs empilhados: use **"Create a merge commit"** na ordem 1 → 10. Com squash, cada
  PR seguinte precisa ser rebaseado (o agente faz isso por você).
- **Incidente real no merge:** ao apagar a branch da fase 1 logo após o merge, o GitHub
  **fechou** o PR #2 (que tinha a fase 1 como base) em vez de apontá-lo para a `main`.
  Recuperação: recriar a branch a partir do commit da fase 1, reabrir o PR #2, trocar a base
  para `main` e só então apagar a branch. Regra que ficou: **troque a base do próximo PR
  antes de apagar a branch.** Nada foi perdido, porque o código estava na branch do #2.

## 6. O que estudar (prioridade)

1. Python: tipos, `dataclass`, pydantic, `Decimal`.
2. Testes com pytest: fixtures, casos de borda, teste de integração.
3. Integração com LLM: saída estruturada (JSON), validação, retry, custo.
4. Eval de sistemas de IA: gabarito, recall, precisão, falso positivo.
5. Git avançado: PRs empilhados, merge commit vs squash vs rebase.

## 7. Perguntas para o NotebookLM

1. Por que o motor de conciliação usa `Decimal` e não `float`?
2. O que acontece quando a IA devolve um JSON fora do formato?
3. Como o sistema impede que a IA invente um valor na contestação?
4. O que é retry com backoff exponencial e quando ele é usado aqui?
5. Por que o eval em modo demo não mede a qualidade da IA?
6. O que é um PR empilhado e qual estratégia de merge usar com ele?
7. Por que o teste que esperava 80 linhas estava errado?

## 8. Referências

- pydantic: https://docs.pydantic.dev
- pytest: https://docs.pytest.org
- Streamlit (testes de app): https://docs.streamlit.io
- Documentação do Python sobre `decimal`: https://docs.python.org/3/library/decimal.html
