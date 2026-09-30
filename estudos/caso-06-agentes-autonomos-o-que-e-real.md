# Caso 06 — Agentes autônomos: o que os podcasts dizem e o que é real no nosso QG

Data: 2026-09-30

## 1. Contexto

Três podcasts gerados pelo NotebookLM (sobre chatbots × trabalhadores digitais, Claude Managed
Agents e agentes autônomos em empresas) foram resumidos pelo Gemini, que concluiu que o QG estava
"100% alinhado" ao estado da arte. Este caso confere o resumo contra o que de fato construímos,
separando o que é verdade, o que é exagero e o que ainda falta.

> Lição de fundo: **resumo gerado por IA também precisa de revisão.** O mesmo cuidado que
> tivemos com o número inventado pelo coach vale para o material de estudo.

## 2. O que fizemos (em ordem)

| Passo | O que aconteceu | Por quê |
|---|---|---|
| 1 | Lemos o resumo dos 3 áudios feito pelo Gemini | Entender o que os podcasts defendem |
| 2 | Conferimos cada afirmação contra o repositório | "Alinhado" precisa de prova, não de impressão |
| 3 | Separamos verdadeiro / exagerado / pendente | Estudar só o que se sustenta |
| 4 | Listamos melhorias em ordem de custo | Fazer primeiro o barato e útil |

## 3. Conceitos

- **Chatbot × trabalhador digital** — o chatbot responde e para; o agente recebe um objetivo e
  repete observar → pensar → agir → avaliar até concluir ou esgotar os recursos.
- **Workflow × agente** — *workflow* é uma sequência fixa de passos com IA em pontos definidos
  (ex: o Coach de Entrevista e o Auditor). *Agente* decide sozinho os próximos passos. Workflow é
  mais previsível e barato; só vale dar autonomia quando o problema exige.
- **Engenharia de contexto** — escolher *o que* entra na janela do modelo: instruções curtas no
  arquivo principal, detalhes em skills carregadas sob demanda, trechos relevantes via busca.
- **Lost in the middle** — com contexto muito longo, o modelo presta menos atenção ao que está
  no meio. Motivo para manter o `AGENTS.md` curto.
- **Memória persistente** — notas fora do modelo (nosso `brain/`) que sobrevivem entre sessões.
- **Consolidação ("dreaming")** — revisar a memória periodicamente: tirar duplicatas, resolver
  contradições, extrair princípios. No QG são as skills `reflect` e `meditate`.
- **Padrão Advisor** — modelos rápidos fazem o trabalho braçal; o modelo mais forte é chamado só
  para decisões difíceis. No QG, a skill `schedule` já roteia: Opus planeja e revisa, Sonnet
  executa, Haiku documenta.
- **Session budget (disjuntor financeiro)** — teto de gasto por tarefa ou chave, para um erro
  em loop não virar conta alta.
- **Tool × skill** — *tool* é a ação bruta (ler arquivo, rodar comando); *skill* é o
  procedimento em Markdown que ensina a sequência certa de usar as tools.

## 4. Por que assim

### O que se confirma no nosso QG

| Conceito | Evidência |
|---|---|
| Instruções curtas + skills sob demanda | `AGENTS.md` enxuto; detalhes em `.agents/skills/` |
| Planejar antes de codar | `brain/plans/<projeto>/brief.md` e `overview.md` antes do código |
| Isolamento | nada direto na `main`; branch e PR por fase; CI em todo PR |
| QG separado dos projetos | QG e `D:\PROJETOS\...` em repositórios diferentes |
| Código verifica a IA | guarda de números inventados no Coach; motor sem IA no Auditor |

### O que o resumo exagerou ou errou

- **Autoria:** o resumo atribui a Lauren Tan a arquitetura dos Claude Managed Agents. Não
  confirmamos. O que é verificável: ela criou o Noodle e o brainmaxxing. Não repita sem fonte.
- **"A engenharia de prompt morreu"** — exagero. Hoje mesmo o prompt do coach melhorou com `[X]`
  e tags anti-injeção. O correto é: prompt sozinho não basta; precisa de contexto e verificação.
- **"Economiza até 80%"** e **"regra das 200 linhas"** — números sem fonte; a segunda é uma
  heurística útil, não uma regra oficial.
- **Analogias forçadas:** *Session* não é a branch diária (sessão é o agente rodando com seu
  histórico; branch é controle de versão). *Events/SSE* é um protocolo de streaming, não
  "feedback no terminal".

### O que ainda não é real (instalado ≠ usado)

- O **loop do Noodle** com worktrees nunca rodou de ponta a ponta (gap 5 do guia).
- **`reflect` e `meditate`** estão instalados, mas ainda não consolidaram nada no `brain/`.
- A **fase 7 do Coach** entrou antes de o plano ser atualizado (o registro veio depois).

## 5. Hacks e pegadinhas

- Quando uma IA disser "você está 100% alinhado", peça a **evidência de cada item**. Se não há
  arquivo, commit ou teste que prove, é intenção, não prática.
- Podcasts gerados por IA soam confiantes mesmo quando misturam fontes. Use-os para ter ideias,
  não como referência final.
- **Disjuntor em 2 minutos:** o OpenRouter permite definir limite de crédito por chave
  (https://openrouter.ai/settings/keys). É o *session budget* na prática.
- Nem todo projeto precisa ser agente autônomo. Saber explicar por que o seu é um workflow
  mostra maturidade numa entrevista.

## 6. O que estudar (prioridade)

1. Workflow × agente: quando dar autonomia a um sistema de IA.
2. Engenharia de contexto: arquivos de instrução, skills, busca de trechos relevantes.
3. Memória de agentes: persistência, consolidação e proteção contra dados maliciosos.
4. Controle de custo: roteamento de modelos e limites de gasto.
5. Orquestração com worktrees (rodar o loop do Noodle de verdade).

## 7. Perguntas para o NotebookLM

1. Qual a diferença entre um chatbot e um trabalhador digital?
2. Por que o Coach de Entrevista é um workflow e não um agente autônomo? Isso é um defeito?
3. O que é "lost in the middle" e como o QG evita esse problema?
4. Qual a diferença entre uma tool e uma skill?
5. Como `reflect` e `meditate` correspondem à ideia de "dreaming"?
6. O que é o padrão Advisor e onde ele já aparece no QG?
7. Por que "estar alinhado" precisa de evidência concreta?
8. Quais afirmações do resumo dos podcasts não puderam ser confirmadas?

## 8. Referências

- Anthropic — Building effective agents: https://www.anthropic.com/engineering/building-effective-agents
- Claude Code — memória e CLAUDE.md: https://docs.claude.com/en/docs/claude-code/memory
- Noodle: https://github.com/poteto/noodle
- OpenRouter — chaves e limites: https://openrouter.ai/settings/keys
