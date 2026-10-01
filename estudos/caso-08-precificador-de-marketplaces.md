# Caso 08 — Precificador de Marketplaces: reescrever, testar e publicar um projeto real

Data: 2026-10-01 · Repositório: `diaquinodev/dashboard-precificacao` (**público**) ·
Demo: https://diaquinodev.github.io/dashboard-precificacao/

## 1. Contexto

Um dashboard de precificação que o usuário fez para consultorias: um único `home.html` de
964 linhas que calcula o preço de venda na Shopee, Mercado Livre, TikTok Shop e Shein. O
objetivo era transformar isso em **projeto de portfólio público**: entender a lógica, conferir
as taxas, corrigir o que estivesse errado, melhorar a tela e documentar para recrutadores.

## 2. O que fizemos (em ordem)

| Etapa | Entrega | Prova de que funciona |
|---|---|---|
| Análise | Leitura do motor + teste em Node das funções extraídas | 5 bugs confirmados com números |
| Taxas | Comparação com fontes de 2026 | TikTok e ML desatualizados; Shopee < R$ 8 diferente |
| Plano | Brief e plano em `brain/plans/dashboard-precificacao/` | 3 fases, 1 PR draft cada |
| Fase 1 | Motor puro: solver por faixa, catálogo CSV, testes | 32 testes, varredura de custo R$ 1–500 |
| Fase 2 | Tela responsiva, importação, cadastro manual, e2e | 13 fluxos no Chrome, 360/768/1280 px |
| Fase 3 | README, docs da planilha, fontes das taxas, arquitetura | prints gerados por script |
| Publicação | Merge, auditoria do histórico, público, GitHub Pages | demo aberta e testada no navegador |

## 3. Conceitos

- **Função pura** — recebe dados e devolve dados, sem mexer na tela nem no disco. O motor
  de preço é todo assim, por isso dá para testar milhares de casos em segundos.
- **Função por partes (piecewise)** — a taxa muda conforme a faixa de preço. Dentro de cada
  faixa a conta é uma reta; entre faixas há "degraus".
- **Solver em forma fechada** — em vez de chutar e ajustar (iteração), resolve a equação de
  cada faixa direto e escolhe a melhor resposta válida. Não tem laço, logo não tem "não
  convergiu".
- **Teste de regressão** — um teste escrito para um bug que já aconteceu, para ele nunca
  voltar sem alguém perceber.
- **Teste de propriedade / varredura** — em vez de 3 exemplos, testa uma regra ("nunca dá
  prejuízo com margem 0%") em 2.000 custos diferentes.
- **E2E (ponta a ponta)** — um robô abre o navegador e usa a tela como uma pessoa.
- **Design tokens** — variáveis com todas as cores, espaços e raios. Trocar o tema = trocar
  as variáveis, não o CSS inteiro.
- **JSDoc + `tsc --noEmit`** — tipos do TypeScript conferidos em arquivos JavaScript, sem
  etapa de compilação.
- **GitHub Pages** — hospedagem grátis de site estático direto de um repositório público.

## 4. Por que assim

- **Motor separado da tela.** O código original misturava conta e HTML. Separando, cada
  regra de preço é testável sem navegador, e a tela só mostra o que o motor calculou. O e2e
  confere que o número na tela é exatamente o do motor.
- **Pasta nova, original intocada.** O arquivo original tinha o nome do cliente e o link da
  planilha de custos. Tudo que entra no git fica no histórico para sempre, então o primeiro
  commit já nasceu anonimizado, e a auditoria olhou o **histórico inteiro** antes de tornar
  público.
- **Sem framework.** Uma tela só não justifica React e build; o diferencial do projeto é o
  motor e os testes.
- **Taxas editáveis com fonte.** Taxa de marketplace muda várias vezes por ano. Em vez de
  prometer números certos, o app mostra a vigência, cita a fonte e deixa editar.

## 5. Hacks e pegadinhas

- **A faixa errada custa caro.** Valor Base R$ 81 na Shopee: com 20% + R$ 4 o preço dá
  R$ 116,44, mas a esse preço a Shopee cobra 14% + R$ 20 → **R$ 9,01 de prejuízo por venda**.
  O certo é R$ 127,85. Esse exemplo virou a abertura do README.
- **`valor || padrão` engole o zero.** Em JavaScript, `0 || 7` dá 7. Era por isso que não dava
  para zerar uma taxa. Correção: tratar "vazio/inválido" separado de "zero".
- **`59.90` vs `59,90`.** Remover todos os pontos antes de trocar a vírgula transforma 59.90
  em 5990. A regra nova está escrita e testada (ponto + 3 dígitos = milhar; senão, decimal).
- **CSV do Excel não é UTF-8.** O Excel em português salva em Windows-1252; sem detectar, "ç"
  vira "Ã§". Solução: tenta UTF-8 estrito e, se falhar, Windows-1252.
- **Título acima do cabeçalho.** Planilhas exportadas costumam ter uma linha de título. O
  detector de separador olhava só a 1ª linha e errava. O teste pegou.
- **Grid CSS e tabela larga.** No celular a página rolava 354 px para o lado: a tabela empurrava
  a coluna do grid. Correção: `grid-template-columns: minmax(0, 1fr)`. Só o teste de
  responsividade viu isso; o print confirmou.
- **Teste passa, tela está feia.** Os testes não viram que "Mais lucrativo" aparecia com lucro
  negativo nem que o selo desalinhava os cartões. **Olhar o print** faz parte da prova.
- **PRs empilhados:** trocar a base do próximo PR para `main` **antes** de apagar a branch
  (lição do caso 04, aplicada sem incidente desta vez).

## 6. O que estudar (prioridade)

1. Testes com `node:test`: `assert`, varreduras e testes de regressão.
2. CSS Grid e Flexbox responsivos; por que `minmax(0, 1fr)` existe.
3. Playwright: localizadores por papel (`getByRole`), `page.route` para simular APIs.
4. Precificação em marketplace: markup × margem, MDR, taxa fixa, frete grátis.
5. Acessibilidade básica: rótulos, `aria-live`, foco visível, cor nunca sozinha.

## 7. Perguntas para o NotebookLM

1. Por que calcular o preço com uma fórmula só dá errado quando a taxa muda por faixa?
2. O que é um solver em forma fechada e por que ele é melhor que uma iteração aqui?
3. Qual a diferença entre markup e margem? Por que o app usa "margem sobre o preço"?
4. Por que `0 || 7` devolve 7 em JavaScript, e como isso virou um bug?
5. Por que separar o "motor" da "tela" facilita testar?
6. O que um teste de ponta a ponta pega que um teste de unidade não pega? E o que nenhum dos
   dois pega?
7. Por que auditar o histórico do git, e não só os arquivos, antes de tornar um repo público?

## 8. Referências

- MDN — CSS Grid `minmax()`: https://developer.mozilla.org/pt-BR/docs/Web/CSS/minmax
- Node.js — Test runner: https://nodejs.org/api/test.html
- Playwright — Locators: https://playwright.dev/docs/locators
- TypeScript — JS Projects Utilizing TypeScript (JSDoc):
  https://www.typescriptlang.org/docs/handbook/intro-to-js-ts.html
- GitHub Docs — GitHub Pages: https://docs.github.com/pages
- Fontes das taxas: `docs/taxas.md` no repositório do projeto.
