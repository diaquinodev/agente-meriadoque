# Dashboard de Precificação

- Problem: seller de moda que vende em 5 marketplaces não sabe que preço anunciar em cada um
  para cobrir comissão, taxa fixa, frete e imposto sem comer o próprio custo. Taxas mudam por
  faixa de preço e mudam ao longo do ano.
- Users: seller/consultoria de e-commerce; recrutador avaliando o portfólio.
- Origin: `D:\PROJETOS\dashboard-precificação\home.html` (feito pelo usuário para consultorias;
  um arquivo, 964 linhas). **Nunca commitar esse arquivo**: tem nome de cliente real e link
  da planilha de custos.
- MVP scope (in):
  - Motor puro e testado: Shopee, ML Clássico/Premium, TikTok Shop, Shein; imposto; markup do
    Valor Base; kit; margem alvo; simulação de desconto; desconto máximo seguro.
  - Catálogo: upload CSV, link CSV do Google Sheets, cadastro manual, catálogo de exemplo
    fictício; relatório de linhas ignoradas.
  - Tela responsiva de verdade (media queries), acessível, design tokens.
  - Docs: README de portfólio, especificação da planilha, fontes das taxas, arquitetura.
  - Público no GitHub + demo no GitHub Pages.
- Out of scope (later): taxa do ML por peso/dimensão (fica valor editável), categorias com
  comissão própria, histórico de preços, backend.
- Chosen approach and why: site estático com ES modules, sem build (roda no Pages e em
  qualquer navegador). JS + JSDoc checado por `tsc --noEmit`. Motor em funções puras,
  separado do DOM, para testar com `node --test`.
- Solver: preço por faixa em forma fechada, P = (custo + fixo) / (1 − % − imposto − margem),
  testando cada faixa e ficando com o menor preço válido. Substitui a iteração antiga, que
  errava na fronteira do TikTok (R$ 79).
- Rejected alternatives and why: React/Next (build e deploy a mais para uma tela só);
  manter Chart.js (uma barra empilhada em CSS mostra a composição do preço com menos peso);
  manter o seletor "Desktop/Mobile" falso (substituído por layout responsivo real).
- Stack: HTML/CSS/JS (ES modules), JSDoc + TypeScript checkJs, ESLint, Prettier, node:test,
  playwright-core com o Edge instalado para o teste de ponta a ponta local.
- Verification (lint/types/tests): `npm run check` = eslint + prettier + tsc + node --test;
  `npm run e2e` (local) = fluxos na tela em 3 larguras + prints.
- Success criteria: os 5 bugs do original têm teste de regressão; margem 0% → lucro extra
  nunca negativo em custo 1–500 (R$ 0,00 salvo quando o preço cai no início de uma faixa); catálogo de exemplo e CSV com `;`/`,` e decimal BR/US
  carregam; sem rolagem horizontal em 360px.
- Open questions: taxas vêm de blogs (fontes secundárias) → usuário confirma no painel de
  vendedor; taxa de transação Shopee de 2% e Campanha Destaque não confirmadas (padrão 0).
