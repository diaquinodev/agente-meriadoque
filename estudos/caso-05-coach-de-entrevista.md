# Caso 05 — Coach de Entrevista: áudio ao vivo, Electron e um agente avaliador

Data: 2026-09-30 · Repositório: `diaquinodev/projeto-pessoal` (privado)

## 1. Contexto

Reconstruir, em uma manhã (prazo 10:30), um projeto perdido: um copiloto de reuniões em
Node/Electron. O foco mudou para **treino de entrevista**: o irmão faz o entrevistador numa
chamada, o app transcreve os dois lados e um agente de IA avalia cada resposta **depois** que
ela termina.

## 2. O que fizemos (em ordem)

| Fase | Entrega | Prova de que funciona |
|---|---|---|
| Brainstorm | Brief: o que volta do projeto antigo, o que muda, o que fica fora | decisões escritas em `brain/plans/coach-de-entrevista/` |
| 1 | Electron seguro + lint, tipos, testes, CI | print automático da janela; CI verde |
| 2 | Captura microfone + loopback, corte por silêncio | voz sintética tocada no PC virou trecho "Entrevistador" |
| 3 | Transcrição Groq com retry | API falsa: 429 → espera → sucesso |
| 4 | Perfil + busca BM25 no currículo | pergunta de "conflito" acha o trecho da mediação |
| 5 | Turnos + agente coach (OpenRouter, Zod) | JSON inválido → 1 correção → válido |
| 6 | Relatório, placar de evolução, flashcards, README | relatório com variação entre sessões |

Total: 56 testes, 6 PRs empilhados, CI em todo PR.

## 3. Conceitos

- **Electron: main × renderer** — o *main* é o processo com acesso ao PC; o *renderer* é a
  janela, uma página web. O *preload* é a ponte controlada entre os dois (`window.coach.*`).
- **contextIsolation / sandbox / CSP** — travas que impedem a página de acessar o Node e
  carregar scripts de fora. Por isso as chaves de API ficam só no main.
- **Loopback** — gravar o som que o PC está tocando, dentro do Windows, antes de chegar ao
  fone. Com fone, a voz do entrevistador não vaza para o microfone.
- **RMS** — o "volume médio" de um pedaço de áudio. O corte por silêncio compara o RMS com
  o ruído de fundo aprendido.
- **AudioWorklet** — código que roda na linha de áudio do navegador e entrega blocos de
  ~21 ms sem travar a tela.
- **WAV 16 kHz mono** — formato leve e suficiente para voz; é o que se manda para transcrever.
- **Alucinação do Whisper** — em silêncio, o modelo "inventa" frases como "Obrigado." ou
  "Legendas pela comunidade Amara.org" (vieram das legendas usadas no treino dele).
- **BM25** — fórmula clássica de busca: dá mais peso a palavras raras e compartilhadas entre
  a pergunta e o trecho. É RAG sem embeddings.
- **Prompt injection** — texto do usuário que tenta dar ordens à IA. Aqui a transcrição vai
  entre tags e o prompt diz para tratá-la como dado.
- **Dublê de teste (fake)** — uma versão falsa da API usada nos testes: permite testar 429,
  503 e JSON errado sem internet e sem chave.

## 4. Por que assim

- **Treino, não cola.** O projeto antigo tinha janela invisível ao compartilhamento, atalho
  de ocultar e teleprompter durante a fala. Isso não foi reconstruído: o feedback só chega
  depois da resposta. Além de ético, é o que protege o portfólio.
- **Código conta, IA julga** (mesma regra do Auditor). Duração e vícios de linguagem são
  contados por código; a IA avalia qualidade e escreve a resposta-modelo.
- **Canal = quem fala.** Em vez de uma IA para descobrir quem falou (diarização), usar dois
  canais físicos resolve de graça.
- **Fase de maior risco primeiro.** A captura por loopback foi a fase 2: se falhasse, havia
  tempo de mudar o plano.
- **Flashcards sem chamada extra.** A resposta-modelo já vem calibrada para ~20 s; virar
  flashcard é só reorganizar dados.

## 5. Hacks e pegadinhas

- O VS Code define `ELECTRON_RUN_AS_NODE=1` nos processos filhos: o Electron abre como Node
  puro, **sem janela e sem erro claro**. Remova a variável antes de `npm start`.
- O npm 11 bloqueia scripts de instalação: o binário do Electron não baixa. Rode
  `node node_modules/electron/install.js`.
- `getDisplayMedia` exige pedir vídeo junto com o áudio; a trilha de vídeo é descartada na hora.
- Testes com voz sintética: a voz do Windows demora ~9 s nesta máquina. Um teste "falhou" só
  porque o print saía antes do silêncio que fecha o trecho — o erro era do teste, não do app.
  **Meça antes de mudar o código.**
- Sem fone, o microfone captou a voz da caixa de som (eco). Solução em duas camadas: fone
  (prática) e descarte de trechos com ≥ 60% de sobreposição com o entrevistador (código).
- **Primeira chamada real ao coach: a IA inventou "80% mais rápido"**, mesmo com o prompt
  proibindo. Causa raiz: o trecho do currículo com o número real ficou em 4º lugar na busca
  e só 3 trechos iam para a IA. Correção em duas camadas: mandar 5 trechos e uma **guarda
  por código** (todo número da resposta-modelo precisa existir na resposta ou no
  currículo; senão, pedir reescrita com `[X]`). Lição: **instrução no prompt não é
  garantia; verificação em código é.**
- Tentativa de filtrar ruído pelo campo `no_speech_prob` da Groq: medido antes de usar,
  ele vem `0,000` até para ruído puro. Não serviu — e medir evitou um filtro inútil.
- Ao ligar o microfone, o chiado inicial virava "fala". Solução: 500 ms de aquecimento só
  medindo o ruído, com teto para que uma fala nunca seja aprendida como ruído.

## 6. O que estudar (prioridade)

1. Electron: processos, IPC, preload e segurança.
2. Web Audio API: `getUserMedia`, `AudioContext`, AudioWorklet.
3. Busca clássica: TF-IDF e BM25 (antes de embeddings).
4. Saída estruturada de LLM com validação (Zod) e estratégias de correção.
5. Prompt injection e como mitigar.

## 7. Perguntas para o NotebookLM

1. Por que as chaves de API ficam no processo main e não na janela?
2. Como o app sabe quem está falando sem usar IA?
3. O que é loopback e por que o fone de ouvido ajuda?
4. Como o corte por silêncio decide onde termina uma fala?
5. Por que o Whisper escreve "Obrigado." quando ninguém falou?
6. O que o BM25 calcula e por que ele basta para um currículo?
7. Por que duração e vícios de linguagem não são avaliados pela IA?
8. Como o prompt se protege de alguém dizer "ignore as instruções" na chamada?
9. Qual a diferença entre este app de treino e um "copiloto invisível" de entrevista?

## 8. Referências

- Electron — segurança: https://www.electronjs.org/docs/latest/tutorial/security
- Web Audio API: https://developer.mozilla.org/docs/Web/API/Web_Audio_API
- Groq — Speech to Text: https://console.groq.com/docs/speech-to-text
- OpenRouter — API: https://openrouter.ai/docs
- BM25 (Wikipedia): https://en.wikipedia.org/wiki/Okapi_BM25
- Zod: https://zod.dev
