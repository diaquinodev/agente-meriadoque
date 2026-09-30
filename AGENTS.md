# AGENTE-MERIADOQUE — Regras para qualquer agente

Vale para Claude (Claude Code / AntiGravity) e Gemini (AntiGravity / Gemini CLI).
Este arquivo é a fonte única de regras. Não crie cópias dele (`GEMINI.md`, `rules.md` etc.).

## O que é este repositório

O "QG" de engenharia do usuário: skills, memória (`brain/`), documentação e estudos.
Os projetos reais (automações, sites, chatbots, extração de dados, telas de app)
vivem em repositórios próprios, criados a partir daqui.

## Quem é o usuário

Estudante de Engenharia de IA e automação, não programador. Aprender com o processo
é tão importante quanto entregar.

- Explique o *porquê* de cada decisão técnica em linguagem simples.
- Termos técnicos: use, mas explique na primeira vez (ex: "worktree (uma cópia
  isolada do projeto para trabalhar sem mexer no original)").
- Quando algo relevante for aprendido, registre em `estudos/` (ver `estudos/README.md`).

## Honestidade e anti-alucinação

- **"Não sei" é resposta válida.** Melhor admitir do que preencher a lacuna com algo plausível.
- **Verifique antes de afirmar:** leia o arquivo, rode o comando ou consulte a documentação.
  Nunca diga "funciona", "testado" ou "passou" sem ter rodado e visto a saída.
- **Nunca invente** números, versões, datas, nomes de pessoas, URLs, nomes de funções, flags
  ou parâmetros de API. Se precisar de um e não tiver fonte, marque `[confirmar]`.
- **Toda afirmação de fato tem origem:** `arquivo:linha`, saída de comando ou URL. Sem origem,
  rotule como **[inferência]** (deduzido) ou **[suposição]** (não verificado).
- **Documento longo:** cite o trecho exato antes de concluir algo sobre ele.
- **Relate o que aconteceu, não o que era esperado:** erro, teste falhando ou passo pulado
  aparecem na resposta, com a saída real.
- **Fim de resposta com fatos novos:** liste o que ficou sem verificação.

## Fluxo de trabalho

1. **Brainstorm** (skill `.agents/skills/brainstorm/`) — interativo, com o usuário. Questionar, confrontar e melhorar a ideia antes de qualquer código.
2. **Plan** (skill `plan`) — transformar a conclusão em plano em fases (`brain/plans/`).
3. **Backlog** — só então a tarefa entra em `todos.md`.
4. **Execução** — loop do Noodle (`execute` → `review`), em branch/worktree, nunca direto na `main`.
5. **Reflect** — registrar aprendizados em `brain/` e, se didático, em `estudos/`.

Nunca pule direto para o código numa ideia nova sem passar pelo brainstorm.

## Fluxo de Pull Requests

O revisor automático (CodeRabbit, plano gratuito) tem cota por hora. PRs são agrupados:

- **Neste repositório (QG):** uma branch por sessão de trabalho (`sessao/AAAA-MM-DD`),
  um commit por assunto, **um único PR no fim da sessão** listando tudo o que mudou.
  O usuário faz o *Squash and merge*.
- **Repositórios de projeto (código):** uma branch e um PR **por fase do plano**. Abra o
  PR como **draft**; marque *Ready for review* só quando a fase estiver pronta e com os
  testes passando. Assim o CodeRabbit revisa uma vez, com tudo pronto.
- Antes de apagar qualquer branch local, confirme que o PR está `MERGED`.

## Onde fica cada coisa

| Pasta/arquivo | Para quê |
|---|---|
| `brain/` | Memória persistente do projeto (princípios, conhecimento, planos). Leia `brain/index.md` antes de agir. |
| `.agents/skills/<nome>/SKILL.md` | Instruções de cada habilidade. Se a tarefa combina com uma skill, leia o `SKILL.md` dela e siga. |
| `docs/` | Documentação para o usuário (guia do agente). |
| `estudos/` | Estudos de caso didáticos (vão para o NotebookLM). |
| `todos.md` | Backlog do Noodle. |
| `.noodle/` | Estado interno do Noodle — nunca edite à mão. |

## Regras do brain

- **Leia primeiro** os arquivos do brain relevantes à tarefa.
- **Escreva** depois de erros, correções ou aprendizados importantes.
- **Estrutura:** um tema por arquivo; índices com `[[wikilink]]`, sem conteúdo inline.
- **Manutenção:** apague notas desatualizadas.

## Não faça

- Não crie arquivos fora da estrutura acima sem combinar com o usuário.
- Não duplique regras deste arquivo em outros lugares.
- Não faça commit direto na `main` de um projeto; use branch + Pull Request.
- Agentes que não são Claude (ex: Gemini) não iniciam o loop do Noodle (`noodle start`).
