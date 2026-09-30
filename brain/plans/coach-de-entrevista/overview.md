# Coach de Entrevista — plano

Brief: [[coach-de-entrevista/brief]] · Prazo: 2026-09-30 10:30 (início 05:10).
Repositório: `diaquinodev/coach-de-entrevista` (privado), local `D:\PROJETOS\coach-de-entrevista`.

## Contexto

Treino de entrevista com o irmão como entrevistador por chamada. Microfone = candidato,
loopback = entrevistador. Transcrição Groq, coach OpenRouter **depois** de cada resposta.

## Arquitetura

- `main` (Node): chaves, chamadas Groq/OpenRouter, arquivos de perfil e sessão.
- `preload`: ponte `window.coach.*` mínima (IPC), `contextIsolation: true`, `sandbox: true`,
  `nodeIntegration: false`.
- `renderer`: captura (mic + loopback), medidores, segmentação por silêncio, tela.
- Núcleo puro em `src/core/` (sem Electron): segmentador, turnos, RAG, prompts, esquemas
  Zod, relatório. Tudo testado com `node --test` e dublês (STT/LLM falsos).

Alternativas: ver brief (Python+terminal, Web Speech, Whisper local rejeitados).

## Fases (1 PR draft por fase)

| # | Fase | Tempo | Prova |
|---|---|---|---|
| 1 | Scaffold Electron seguro + ESLint/Prettier/tsc(checkJs)/node --test + CI | 30 min | janela abre; CI verde |
| 2 | Captura mic + loopback, medidores, segmentador por silêncio (núcleo testado) | 50 min | teste do segmentador; trechos rotulados na tela |
| 3 | STT Groq no main (retry 429, dublê), transcrição ao vivo na tela | 35 min | teste com dublê; 1 chamada real |
| 4 | Perfil (nome, cargo, gênero, currículo .md) + RAG lexical | 30 min | teste: pergunta acha trecho certo |
| 5 | Turnos + coach OpenRouter (3 réguas, Zod, 1ª pessoa, gênero, STAR 20s) | 50 min | teste com LLM falso + JSON inválido; 1 chamada real |
| 6 | Histórico JSON, relatório .md, flashcards, placar, README, estudo 05 | 35 min | sessão fictícia gera relatório |

Corte de emergência (nesta ordem): placar → flashcards → tipo Gestor.

## Verificação

`npm run check` = `eslint . && prettier --check . && tsc --noEmit && node --test`.
Teste ao vivo final: chamada com o irmão, fone, uma pergunta de cada tipo.

## Status

- 2026-09-30 05:10 — brief aprovado, fase 1 iniciada.
