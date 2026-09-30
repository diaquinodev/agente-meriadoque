# Coach de Entrevista

Reconstrução, com foco **só em treino de entrevista**, de um projeto anterior do usuário
(copiloto de reuniões em Node/Electron, perdido). Nome, marca e textos próprios.

- **Problem:** treinar entrevista sem retorno não ensina. Você responde e não sabe se foi
  claro, se usou exemplo concreto, se citou o que está no seu currículo.
- **Users:** o usuário (candidato), com o irmão fazendo o entrevistador por chamada.
- **Cenário:** irmão e usuário em PCs diferentes, numa chamada (Meet/Discord). Usuário de
  **fone de ouvido**: a voz do irmão chega pelo **loopback** (som do sistema, capturado
  dentro do Windows, antes do fone); a voz do usuário, pelo **microfone**, sem eco. O canal
  identifica quem fala.
- **MVP scope (in):**
  1. Janela Electron com seleção de microfone e medidores de volume por canal.
  2. Captura de microfone (`getUserMedia`) e loopback (`getDisplayMedia` com áudio
     `loopback`), segmentação por silêncio (detecção de voz pelo volume) em cada canal.
  3. Transcrição de cada trecho pela **Groq Whisper** (`whisper-large-v3-turbo`), pt-BR,
     rótulo "entrevistador"/"candidato".
  4. Perfil local: nome, cargo-alvo, gênero gramatical, currículo em Markdown.
  5. **RAG lexical** sobre o currículo: busca os trechos relacionados à pergunta.
  6. Detecção de turno: pergunta (entrevistador) → resposta (candidato) → fim da resposta
     quando o entrevistador volta a falar ou pelo botão "terminei".
  7. **Agente coach** via OpenRouter, **depois** de cada resposta: nota, checagem STAR,
     pontos fortes, o que melhorar, vícios de linguagem, o que do currículo faltou citar e
     uma resposta-modelo para estudo (com concordância de gênero). Saída JSON validada
     com Zod.
  8. Relatório da sessão em Markdown + flashcards STAR (resposta de ~20s) para revisar.
  9. **Tipo de entrevista** escolhido no início (Técnica / Gestor / RH): muda a régua do
     coach e o formato da resposta-modelo.
  10. Histórico salvo por sessão (`AAAA-MM-DD-HHmmss-interview.json`) e **placar de
      evolução** (nota média por sessão) no relatório.
- **Engenharia de prompt (herdada do projeto antigo, aplicada depois da resposta):**
  - Resposta-modelo em **1ª pessoa**, com concordância de gênero do perfil, usando os
    projetos do currículo (trechos do RAG).
  - Sem enrolação: proibido saudação/introdução genérica; o coach também aponta enrolação
    na resposta do candidato.
  - Comportamental (RH/Gestor): STAR calibrado para ~20s de fala; o coach mede a duração
    real da resposta (tempo do trecho de áudio).
  - Técnica: resposta-modelo em três blocos (Abordagem / Código ou Query / Complexidade
    Big-O); o coach verifica se o candidato explicou a abordagem e citou a complexidade.
  - Gestor: trade-offs, riscos, decisões; o coach cobra justificativa de negócio.
- **Adicionado em 2026-09-30 (fase 7), a pedido do usuário:** falas do entrevistador começam
  como contexto (não vão para a IA); o usuário marca a pergunta (clique ou tecla P); **Modo
  estudo** mostra roteiro e resposta-modelo ao marcar a pergunta. Motivo: apoio no
  aprendizado para quem trava por ansiedade. Salvaguardas: janela visível, sem atalho global,
  opcional, e o relatório registra cada resposta dada com apoio (placar mostra a evolução até
  responder sem apoio).
- **Out of scope (later):** compressão de contexto (medir antes de otimizar),
  MCP Bridge/handoff/deep-link, roteamento entre vários provedores
  (fica só a interface pronta para isso), Whisper local, modo "mesma sala", voz sintética.
- **Recusado por princípio (não será construído):** janela invisível ao compartilhamento
  de tela (`setContentProtection`/`WDA_EXCLUDEFROMCAPTURE`), atalho de ocultar, captura
  silenciosa de tela (inclusive para live coding: no treino o irmão lê o problema) e
  teleprompter com resposta pronta durante a fala. O feedback só
  chega **depois** da resposta. Ferramenta de treino, não de cola.
- **Chosen approach and why:** Electron (escolha do usuário: janela real, próximo do
  projeto antigo, bom para portfólio). Chaves e chamadas de API só no processo **main**; a
  janela (renderer) fala com ele por `preload` + `contextIsolation`, sem acesso a Node nem
  às chaves. STT na Groq porque a Web Speech API não funciona de forma confiável no
  Electron e não transcreve o loopback.
- **Rejected alternatives and why:** Python + terminal (sem janela; o usuário quer
  desktop); Web Speech API (depende de chave interna do Chrome, só escuta o microfone);
  Whisper local (lento demais na CPU de 2012 para o prazo); TypeScript com build
  (mais configuração; usamos JavaScript com checagem de tipos via JSDoc + `tsc --noEmit`).
- **Stack:** Node 24, Electron, JavaScript ESM com JSDoc, Zod, `fetch` nativo para Groq e
  OpenRouter (APIs compatíveis com OpenAI). Dev: ESLint, Prettier, `tsc --noEmit`
  (checkJs), `node --test`, GitHub Actions.
- **Verification (lint/types/tests):** ESLint + Prettier, `tsc --noEmit`, `node --test`
  (segmentação, turnos, RAG, validação do coach com IA e STT falsos), CI em todo PR.
- **Success criteria:**
  1. Chamada real com o irmão: fala dele aparece como "entrevistador", a sua como
     "candidato".
  2. Feedback do coach em até ~10s depois do fim da resposta.
  3. O coach cita algo do currículo que faltou na resposta.
  4. Relatório + flashcards salvos no fim da sessão.
  5. Testes e CI verdes sem chave nenhuma (dublês).
- **Constraints:** prazo 4h; Windows 10, CPU i7-3632QM, 8 GB RAM; chaves só em variáveis
  de ambiente (`OPENROUTER_API_KEY`, `GROQ_API_KEY`), nunca no chat nem no repositório;
  modelos/planos gratuitos podem usar os dados → nada sensível no currículo de teste;
  retry com backoff em erro 429; modelo configurável (`OPENROUTER_MODEL`).
- **Open questions / risks:**
  - Loopback via `getDisplayMedia` no Electron/Windows é a parte de maior risco → fica numa
    fase só, cedo; se travar, o plano B é o usuário escolher manualmente a saída de áudio.
  - Consentimento: irmão sabe e concorda que a voz é transcrita (o áudio vai para a Groq).
