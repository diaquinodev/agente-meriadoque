# Prompt mestre — configurar o Cursor no QG

Para colar **uma vez** no agente do Cursor (painel de chat, modo *Agent*), com a pasta
`AGENTE-MERIADOQUE` aberta. O prompt faz o agente **diagnosticar primeiro**, mostrar provas e
**pedir sua aprovação** antes de mudar qualquer arquivo.

> Antes de colar: no Cursor, abra *Settings → Rules, Skills, Subagents* e ligue
> *Include third-party Plugins, Skills, and other configs*. É o que permite ao Cursor usar
> os hooks do Claude (`.claude/settings.json`). O agente não consegue ligar isso por você.

## Prompt (copie tudo dentro da caixa)

```text
Você vai validar e, só com a minha aprovação, ajustar este repositório para funcionar no
Cursor com o mesmo fluxo que já funciona no Claude Code e no Antigravity. Não reconstrua
nada: a estrutura já existe e é a fonte da verdade.

## 0. Leia antes de qualquer ação (obrigatório)
1. AGENTS.md (regras; valem acima deste prompt se houver conflito)
2. brain/index.md e, dele, brain/codebase/windows-toolchain-gotchas.md
3. docs/GUIA-DO-AGENTE.md e docs/MANUAL.md
4. .claude/settings.json, .claude/hooks/inject-brain.sh, .claude/hooks/auto-index-brain.sh
5. .noodle.toml e a lista de pastas em .agents/skills/
Responda com uma lista dos arquivos que você leu de fato. Não resuma o que não abriu.

## Regras desta tarefa (além do AGENTS.md)
- Nada de alucinação: toda afirmação de fato vem com origem (arquivo:linha, saída de comando
  ou URL da documentação oficial do Cursor). Sem origem, marque [inferência] ou [suposição].
  Não sabe? Escreva "não sei" ou [confirmar].
- Nunca diga "funciona" ou "passou" sem rodar e mostrar a saída real.
- NÃO crie cópias das regras: nada de GEMINI.md, rules.md, .cursorrules nem .cursor/rules/
  repetindo o AGENTS.md. Não use /create-rule para isso.
- NÃO crie arquivos ou pastas novas (inclusive .cursor/) sem eu aprovar o arquivo e o motivo.
- NÃO rode `noodle start`. O Noodle eu rodo no terminal com o Claude.
- Git: trabalhe só na branch de sessão `sessao/AAAA-MM-DD` (se a de hoje não existir, pergunte
  antes de criar). Nunca commit na main. Não mexa em alterações que já estavam pendentes
  antes de você começar (rode `git status` no início e mostre). Um commit por assunto.
  Não abra Pull Request: só informe que a sessão está pronta para o PR.
- Explique cada decisão em linguagem simples; sou estudante, não programador. Termo técnico
  novo vem com explicação curta na primeira vez.

## Fase 1 — Diagnóstico (somente leitura, sem editar nada)
Verifique e mostre a prova de cada item:
1. Regras: o AGENTS.md (e o CLAUDE.md, que só importa o AGENTS.md) estão carregados como regra?
2. Skills: liste as skills que você carregou. Esperado: brain, brainstorm, execute, meditate,
   noodle, plan, reflect, review, ruminate, schedule (as pastas de .agents/skills/).
   Atenção: .claude/skills é um link simbólico para .agents/skills. Verifique se o Cursor
   carregou cada skill duas vezes; se sim, relate (não corrija ainda).
3. Hooks: o Cursor reconheceu os hooks de .claude/settings.json (SessionStart e PostToolUse)?
   Mostre onde você viu isso (log de hooks do Cursor, configuração, documentação). Confira na
   documentação oficial (https://cursor.com/docs/reference/third-party-hooks):
   a) se a saída em texto simples do inject-brain.sh é aceita ou rejeitada;
   b) se a variável CLAUDE_PROJECT_DIR existe quando o Cursor roda o hook;
   c) se o matcher "brain/" do PostToolUse funciona do mesmo jeito.
4. Terminal: rode no terminal do Cursor (PowerShell) e mostre a saída de:
   git --version; node --version; python --version; claude --version; noodle --version;
   echo $env:ELECTRON_RUN_AS_NODE
   Algum comando não encontrado = relate, não instale nada.
5. Git: branch atual e `git status`.

Entregue uma tabela: Item | Esperado | Encontrado | Prova | OK/Problema.
PARE AQUI e espere minha resposta.

## Fase 2 — Proposta de ajustes (ainda sem editar)
Para cada problema da Fase 1, proponha a menor correção possível:
- o que mudar, em qual arquivo, e por quê;
- o risco de quebrar o Claude Code (que usa os mesmos hooks), e como testar nos dois;
- alternativas (incluindo "não mudar nada", quando o AGENTS.md já cobre o problema; ex.: ele
  já manda ler brain/index.md antes de agir).
PARE e espere eu aprovar item por item.

## Fase 3 — Execução (só o que eu aprovei)
- Aplique uma mudança por vez e teste com saída real logo em seguida.
- Se mexer em hook, prove que continua funcionando no Claude Code também (ou diga claramente
  que não conseguiu testar lá e que eu preciso testar).
- Atualize a documentação que cita o Antigravity, só nos trechos necessários:
  docs/GUIA-DO-AGENTE.md (seção de regras e seção "Terminal + AntiGravity"), docs/MANUAL.md
  (Etapa 0), brain/codebase/windows-toolchain-gotchas.md (linha da IDE e
  ELECTRON_RUN_AS_NODE, se você confirmou na Fase 1). Cursor entra como opção, sem apagar o
  Antigravity.
- Commit por assunto na branch de sessão, com mensagem no padrão do histórico (`git log -5`).

## Fase 4 — Relatório final
1. O que mudou (arquivo e motivo) e o que ficou como estava.
2. Teste final: abra uma conversa nova no Cursor e confirme regras + 10 skills carregadas.
3. Seção "Não verificado:" com tudo que ficou sem prova.
4. Sugestão de texto para um estudo de caso em estudos/ (não crie o arquivo; eu decido).

Comece pela etapa 0.
```

## Por que o prompt é dividido em fases

- **Diagnóstico antes de mudar:** a maior parte já funciona no Cursor (ele lê o `AGENTS.md` e
  `.agents/skills/` sozinho). Mexer antes de medir é arriscar quebrar o que funciona.
- **Pontos de parada:** você aprova cada mudança. Isso segue a regra "brainstorm antes de código".
- **Hooks são compartilhados:** os mesmos scripts servem o Claude Code. Uma correção feita só
  pensando no Cursor pode quebrar o Claude; por isso o prompt exige testar nos dois.
