# Matriz de Verificação

Back to [[plans/estudio-fotos-marketplace/overview]]

## Definition of Done
- Cada artefato novo aparece no ledger com teste, evidência e resultado.
- Cada botão é exercitado por nome acessível; cliques em `div` não contam como controle.
- Cada regra possui teste no nível mais baixo possível e confirmação no fluxo real.
- Mocks comprovam lógica; somente smoke test prova integração; somente revisão visual prova fidelidade.

## Static Gates
- `npm run lint`; `npm run typecheck`; `npm test`; `npm run build`.
- Migrações sobem do zero e aplicam RLS; nenhuma chave secreta chega ao cliente.

## Button and Artifact Ledger
- Entrada: Modelo, Still, Kits, continuar sessão e nova sessão.
- Referências: enviar, trocar, remover, frente, costas, detalhe, dicas, avançar e voltar.
- Ficha: confirmar, corrigir, baixa confiança e criar nova versão.
- Modelo: corpo/tamanho, pele, cabelo, styling, fundo, gerar/aprovar/rejeitar/regenerar teste.
- Pacotes: selecionar 3/5/10, ver tomadas/custo, gerar, pausar e retomar.
- Kits: adicionar, remover, reordenar, nomear cor e escolher composição.
- Revisão: comparar, zoom, aprovar, rejeitar, retoque, retry, baixar e excluir.
- Global: menu móvel, galeria, créditos, logout, modais, erros e restauração.

## Rule Matrix
- Identidade: mesma referência e sem alteração facial/corporal na sessão.
- Peça: cor, estampa, corte, logos, recortes e detalhes críticos preservados.
- Costas: indisponível como fiel sem referência traseira.
- Complemento: neutro, sem logo/estampa e sem cobrir o produto principal.
- Conjunto: componentes tratados como uma unidade.
- Still: frontal, manequim fantasma e nenhuma parte humana.
- Kit: quantidade, cor e identidade exatas; sem duplicação ou mistura.
- Aprovação: saída nasce pendente; somente decisão humana libera exportação.
- Dimensão: arquivo entregue mede exatamente `1200 x 1200`.
- Isolamento: nenhum dado cruza usuário, sessão ou estúdio.
- Créditos: retry idempotente, concorrência segura e falha sem cobrança indevida.
- Exclusão: banco, Storage e cache convergem para removidos.

## Runtime Evidence
- Playwright desktop e mobile; axe; screenshots dos estados principais e erros.
- PostgreSQL real para concorrência, RLS, idempotência, migrações e exclusão.
- Provedor real com conjunto pequeno e controlado de peças de referência.
- Relatório final lista tempos, custo por geração, rejeições, divergências e itens não verificados.
