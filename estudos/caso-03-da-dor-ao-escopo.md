# Caso 03 — Da dor ao escopo: o brainstorm do Auditor de Repasses

Data: 2026-09-29

## 1. Contexto

Objetivo: criar um case de portfólio com orquestração de agentes, funcionando de ponta
a ponta, resolvendo uma dor real de negócio e chamando a atenção de tech recruiters.

## 2. O que fizemos (em ordem)

| Passo | O que aconteceu | Por quê |
|---|---|---|
| 1 | Definimos o que avaliadores procuram antes de escolher a ideia | Evita construir algo bonito que não prova nada |
| 2 | Comparamos 3 ideias genéricas | Ter alternativas evita se apaixonar pela primeira |
| 3 | O usuário trouxe a experiência em **varejo** | Conhecimento de domínio vale mais que qualquer ideia genérica |
| 4 | Surgiram 2 dores reais: conciliação de taxas e reembolso travado | Dor vivida > dor imaginada |
| 5 | Escolhemos **uma** dor para o MVP | Dois problemas ao mesmo tempo = portfólio pela metade |
| 6 | Usamos um teste de vaga real para descobrir o que a área valoriza | Os conceitos do teste viraram funcionalidades |
| 7 | Separamos o que é tarefa de IA e o que é tarefa de código | Maturidade de engenharia |
| 8 | Registramos o brief em `brain/plans/auditor-de-repasses/brief.md` | A decisão fica escrita e revisável |

## 3. Conceitos

- **MDR (Merchant Discount Rate)** — a taxa que o gateway ou adquirente cobra por
  transação. Ex: 2% de R$ 1.000 = R$ 20.
- **Antecipação de recebíveis** — receber hoje um dinheiro que só cairia em 30+ dias,
  pagando uma taxa. Costuma variar por número de parcelas.
- **Ciclo de pagamento (settlement)** — a regra de *quando* o dinheiro cai.
  Ex: vendas de 1 a 15 são pagas no dia 25.
- **Conciliação** — comparar o que deveria acontecer (contrato) com o que aconteceu
  (repasse) e explicar cada diferença.
- **Chargeback** — o cliente contesta a compra no cartão e o valor é estornado.
- **Divergência favorável** — o parceiro cobrou *menos* do que devia. Também precisa ser
  apontada: pode ser cobrada de volta ou revelar um erro de configuração.
- **Eval** — uma "prova" com respostas conhecidas para medir quanto o sistema acerta.
- **Human-in-the-loop** — um ponto do fluxo em que um humano aprova antes de seguir.
- **MVP** — a menor versão que já entrega valor de verdade.

## 4. Por que assim

- **IA para texto e julgamento, código para conta.** LLMs erram aritmética; conciliação
  exige exatidão. O motor que compara valores é código comum e testado. Os agentes leem
  o contrato, investigam causas e redigem contestações.
- **Cada agente precisa de um motivo para existir.** "Multiagente porque é moda" é a
  primeira coisa que um avaliador experiente questiona.
- **Dados fictícios com divergências plantadas.** Resolve a privacidade (LGPD) e cria o
  eval: como sabemos o que foi plantado, conseguimos medir o acerto.
- **Ética com material de processo seletivo.** Usamos os *conceitos* do teste, nunca o
  texto, a marca ou o nome da área no projeto público.
- **Streamlit no MVP.** Uma linguagem só (Python) e entrega mais rápida; a interface
  mais elaborada (Next.js) fica para depois.

## 5. Hacks e pegadinhas

- Antes de uma entrevista, pesquise o **vocabulário da área** (Fee Assurance,
  Settlement, MDR). Um case que usa as mesmas palavras da vaga conversa direto com
  quem avalia.
- API gratuita: use só dados fictícios (o provedor pode usar seus dados) e trate o
  limite de requisições (esperar e tentar de novo).
- Deixe o sistema independente do provedor de IA: trocar Gemini por outro deve ser uma
  linha de configuração.

## 6. O que estudar (prioridade)

1. Fundamentos de meios de pagamento: MDR, antecipação, parcelamento, chargeback.
2. Python + pandas: ler CSV, comparar colunas, gerar Excel.
3. Saída estruturada de LLM (JSON validado por schema).
4. Como montar um eval simples: casos de teste, acerto, falso positivo, falso negativo.
5. Padrões de orquestração: pipeline sequencial com aprovação humana.

## 7. Perguntas para o NotebookLM

1. Por que o motor de conciliação não usa IA?
2. Qual o papel de cada um dos 3 agentes e por que não fundi-los em um só?
3. Por que uma divergência a favor da empresa também deve ser reportada?
4. Como dados fictícios com divergências plantadas permitem medir o sistema?
5. O que é falso positivo e falso negativo numa conciliação?
6. Por que escolher uma dor só para o MVP?
7. Como usar um teste de vaga de forma ética para montar um portfólio?

## 8. Referências

- Documentação do pandas: https://pandas.pydata.org/docs/
- Documentação do Streamlit: https://docs.streamlit.io
- Documentação do pydantic: https://docs.pydantic.dev
