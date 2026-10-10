# Estúdio de Fotos para Marketplace — Plano

## Context
- O protótipo mistura Modelo, Still e Kits no mesmo estado, aprova imagens automaticamente e não possui ficha imutável da peça nem identidade visual bloqueada.
- O objetivo é um produto real para pequenas marcas de moda, com fidelidade da peça como promessa principal e economia de tempo como benefício secundário.
- O plano parte do brief em [[plans/estudio-fotos-marketplace/brief]] e da auditoria do código atual.

## Scope
- Inclui três estúdios independentes, ficha confirmável, foto teste, identidade fixa, pacotes determinísticos, Still frontal, Kits, revisão humana, persistência auditável e experiência responsiva.
- Inclui teste de todo artefato, botão e regra conforme [[plans/estudio-fotos-marketplace/testing]].
- Exclui vídeo, publicação direta em marketplaces, cobrança real e modelos reutilizáveis entre sessões.

## Constraints and Decisions
- Escolhida: três rotas/fluxos independentes com componentes compartilhados; evita condicionais e estado residual do wizard atual.
- Rejeitada: manter um wizard adaptativo único; o código atual já demonstra vazamento de regras entre modos.
- Adiada: workbench profissional; maior densidade e pior primeira experiência em celular.
- `garment_truth` e identidade aprovada são imutáveis dentro da geração; cenário, pose e acessórios nunca têm prioridade sobre eles.
- Sem foto traseira, costas não podem ser rotuladas como fiéis; usar ângulo 3/4.
- Decisão posterior ao plano original: usar a OpenRouter Image API com `openai/gpt-image-2.5-sunburst`, escolhida para edição precisa com imagens de referência. A Fase 1 exige um smoke test real pela rota da aplicação e uma matriz de capacidades por modelo; configurações não suportadas não podem ser enviadas.
- Limite inicial assumido para Kits: três itens; alterar somente com evidência de qualidade.

## Applicable Skills
- `execute` para cada fase em branch/worktree isolada.
- `review` antes de concluir cada fase.
- `reflect` antes de abrir o PR final da sessão do QG.

## Phases
1. [[plans/estudio-fotos-marketplace/phase-01-provider-baseline]]
2. [[plans/estudio-fotos-marketplace/phase-02-domain-contracts]]
3. [[plans/estudio-fotos-marketplace/phase-03-api-boundary]]
4. [[plans/estudio-fotos-marketplace/phase-04-prompt-engines]]
5. [[plans/estudio-fotos-marketplace/phase-05-generation-accounting]]
6. [[plans/estudio-fotos-marketplace/phase-06-data-lifecycle]]
7. [[plans/estudio-fotos-marketplace/phase-07-ux-shell]]
8. [[plans/estudio-fotos-marketplace/phase-08-garment-intake]]
9. [[plans/estudio-fotos-marketplace/phase-09-still-engine]]
10. [[plans/estudio-fotos-marketplace/phase-10-model-lock]]
11. [[plans/estudio-fotos-marketplace/phase-11-model-packages]]
12. [[plans/estudio-fotos-marketplace/phase-12-kits-engine]]
13. [[plans/estudio-fotos-marketplace/phase-13-review-gallery]]
14. [[plans/estudio-fotos-marketplace/phase-14-product-audit]]

## Project Verification
- `npm ci`, `npm run lint`, `npm run typecheck`, `npm test` e `npm run build`.
- Testes PostgreSQL/RLS com `TEST_DATABASE_URL` e migrações aplicadas em banco descartável.
- Playwright em desktop e mobile, axe, screenshots e fluxo completo por estúdio.
- Smoke test pago/controlado do provedor e avaliação humana de fidelidade; mocks não comprovam qualidade visual.
