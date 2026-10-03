# Inteligência de Compra — plano

Brief: [[inteligencia-de-compra/brief]]. Repo: `diaquinodev/inteligencia-de-compra` (privado até
a fase 7; público depois de auditoria). Pasta: `D:\PROJETOS\inteligencia-de-compra`.
GCP: projeto `inteligencia-de-compra` (nº 26983533402), BigQuery **sandbox** (sem cartão).

## Contexto

Projeto de portfólio para a trilha Analista de Dados: responder, com compras públicas federais
(substituto declarado de um ERP corporativo), onde está o gasto, onde se paga caro e quanto dá
para economizar. Fecha a lacuna de evidência em BigQuery, SQL analítico, Data Quality, modelo
estrela e Power BI. IA entra só depois de o MVP se sustentar sozinho.

## Escopo

- **Dentro (este plano):** fases 0–7 (MVP de dados, do acesso ao dado até o README público).
- **Fora (planos futuros, só esboço no fim):** padronização por IA, Agente Auditor, pipeline
  agendado e dbt.

## Restrições e decisões

- **Sandbox:** 1 TiB/mês de consulta, 10 GiB de armazenamento, tabelas expiram em 60 dias,
  sem DML. Consequência: toda tabela nasce de `CREATE OR REPLACE TABLE ... AS SELECT`, e um
  comando recria tudo do zero (idempotente; ver [[principles/make-operations-idempotent]]).
- **Trava de custo no executor:** toda consulta passa por *dry run* (estimativa de bytes) e por
  um teto de bytes por consulta. Regra em código, não em instrução
  ([[principles/encode-lessons-in-structure]]). As tabelas públicas têm dezenas de milhões de
  linhas; um `SELECT *` descuidado gasta a cota do mês.
- **Recorte cedo:** a primeira camada materializa só a categoria e os anos escolhidos; tudo o
  que vem depois lê do recorte, não da tabela pública.
- **Acesso ao BigQuery** — alternativas: (a) login do usuário via `gcloud` (credencial local
  padrão); (b) fluxo de login no navegador por biblioteca Python; (c) chave de conta de
  serviço. **Escolha: (a)** — é o caminho padrão, sem arquivo de chave para vazar. O CI do MVP
  não acessa o BigQuery (roda lint e testes do executor); acesso agendado fica para o plano
  futuro. [confirmar na fase 0 que o sandbox aceita (a)]
- **ELT em SQL puro**, um arquivo por tabela, ordem pela numeração dos arquivos; Python só
  executa. dbt rejeitado para o MVP (ver brief).
- **Lógica onde é testável:** regras de qualidade e métricas vivem em SQL versionado; o Power
  BI só apresenta. Medidas DAX ficam documentadas em arquivo de texto no repo, porque o
  `.pbix` é binário e não aparece em diff.
- **Aprendizado é critério de pronto:** cada fase termina com uma nota "como explicar" (o que
  a consulta faz, por que assim, o que o dado não permite concluir) e o usuário a revisa.
- **Power BI é montado pelo usuário**, com roteiro passo a passo; o agente não opera a
  ferramenta.

## Skills aplicáveis

Nenhuma skill instalada cobre BigQuery ou Power BI. Usar `execute` e `review` por fase;
`reflect` ao fim de cada sessão.

## Fases (1 PR draft por fase; merge só do PR cuja base é `main`)

| # | Fase | Arquivos principais | Prova |
|---|---|---|---|
| 0 | **Validação dos dados** (interativa, com o usuário): instalar `gcloud`, login, primeira consulta; mapear tabelas e colunas reais; medir cobertura de código de catálogo, preço unitário e quantidade; escolher categoria e anos; decisão seguir/plano reserva | `docs/fase-0-validacao.md` (consultas e resultados reais) | consulta roda da máquina do usuário; números de cobertura registrados; decisão escrita |
| 1 | **Scaffold**: repo, estrutura de pastas, executor (lê `sql/` em ordem, dry run, teto de bytes, `CREATE OR REPLACE`), config do projeto/dataset, `sqlfluff`, `pytest`, CI | `src/executor.py`, `tests/`, `.sqlfluff`, `.github/workflows/ci.yml` | testes do executor (ordem, teto, falha clara); CI verde; `python -m ... --dry-run` mostra bytes |
| 2 | **Camada bruta e limpa**: recorte materializado; tipos, datas, CNPJ normalizado, unidade de medida, valores monetários | `sql/10_raw_*.sql`, `sql/20_stg_*.sql` | contagem de linhas bruto × limpo explicada; rodar duas vezes dá o mesmo resultado |
| 3 | **Data Quality**: testes como consultas que devem voltar zero linhas (CNPJ válido, preço > 0, chave única, unidade conhecida, datas no intervalo) + relatório de quantas linhas cada regra barrou | `sql/tests/*.sql`, `src/qualidade.py`, `docs/qualidade.md` | teste falha com dado ruim plantado; relatório com números reais |
| 4 | **Modelo estrela**: `fato_item_compra`; dimensões fornecedor, item, órgão, tempo; chaves substitutas | `sql/30_dim_*.sql`, `sql/40_fato_*.sql`, `docs/modelo.md` (diagrama) | integridade: toda linha da fato acha suas dimensões; total da fato = total da camada limpa |
| 5 | **Análises**: (1) concentração de gasto por categoria/fornecedor; (2) dispersão de preço por item (mediana, percentis, ranking com window functions); (3) economia potencial ao preço mediano | `sql/50_mart_*.sql`, `docs/analises.md` | cada número do README sai de uma consulta do repo; conferência manual de 3 itens |
| 6 | **Power BI**: conexão ao BigQuery, relacionamentos, medidas DAX, 3 páginas (gasto, preço, economia), publicação | `powerbi/inteligencia-de-compra.pbix`, `powerbi/medidas.md`, `docs/img/` | totais do painel batem com as consultas da fase 5; link público ou `.pbix` + prints + vídeo |
| 7 | **README e publicação**: achados, decisão com valor em R$, limites do dado, como reproduzir; auditoria do histórico; repo público | `README.md` | pessoa de fora reproduz pelo README; CI verde na `main`; nenhum segredo no histórico |

Ordem pelo [[principles/foundational-thinking]]: validação e scaffold antes de qualquer
análise; cada fase deixa o projeto apresentável.

## Verificação do projeto

- Estática: `sqlfluff lint sql/`; `pytest`; CI verde.
- Em execução: um comando recria todas as tabelas e roda os testes de qualidade; os totais do
  painel conferem com as consultas; o usuário explica cada consulta e cada medida.

## Depois do MVP (planos próprios, a detalhar)

- Padronização de descrições por IA, com gabarito e taxa de acerto; medir aumento de cobertura.
- Agente Auditor com ferramentas somente-leitura e aprovação humana.
- Pipeline agendado (GitHub Actions) e migração para dbt.

## Status

- 2026-10-03 — brainstorm concluído e plano escrito. Projeto GCP criado pelo usuário.
  Pendente (usuário): instalar Power BI Desktop. Próximo: fase 0.
- 2026-10-03 — **fase 0: dados servem (seguir com o caminho B).** `gcloud` 587.0.0 instalado;
  login de usuário funciona no sandbox (`bq query` rodou da máquina). Fonte escolhida:
  `basedosdados.br_mgi_compras_publicas.contratacao_item` (8.119.424 linhas, 4,29 GB) +
  `catalogo_material`, `fornecedor`, `orgao`. Cobertura 2024–2025: código de catálogo ~93%,
  preço e quantidade do resultado ~79%, unidade de medida 100%. Itens de material com código
  e preço em 2024–2025: 3,4 milhões de linhas, 139.611 itens distintos, 3.368 órgãos.
  Achados para Data Quality: `codigo_grupo` vem nulo na tabela de itens (grupo só pelo
  catálogo); total de 2024 soma R$ 6,1 trilhões (linhas absurdas, até R$ 4,9 bi num item).
  Recorte proposto (aguarda ok do usuário): grupos 75 (escritório, 186.618 linhas), 70 (TIC,
  77.674) e 79 (limpeza, 72.750), anos 2024–2025 — "compras indiretas". `INFORMATION_SCHEMA`
  do projeto `basedosdados` não é acessível; usar `bq ls`/`bq show`. No Windows, `bq.cmd` falha
  a partir do Git Bash (espaço no caminho) e o pipe do PowerShell injeta BOM: rodar com
  `cmd /c "bq query ... < arquivo.sql"`. Pendente: confirmar recorte, criar o repo (fase 1) e
  levar as consultas da fase 0 para `docs/fase-0-validacao.md`.
- 2026-10-03 — **fases 1–7 escritas, PRs draft #1–#7 empilhados** em
  `diaquinodev/inteligencia-de-compra` (privado). Usuário autorizou executar até o fim.
  Ordem real das fases mudou: 3 modelo, 4 análises, 5 qualidade (os testes leem a fato e os
  marts). Resultado: 18 tabelas recriadas do zero em ~1 min (~650 MiB estimados); fato com
  334.901 linhas e R$ 15,8 bi; 11/11 testes de qualidade; 5 regras falham com 3 erros
  plantados; 10 testes do executor; ruff, mypy e sqlfluff limpos na máquina. Economia: teto
  R$ 2.269,8 mi, conservadora R$ 1.074,9 mi, defensável R$ 263,7 mi (grupos de preço
  homogêneo, p75 ≤ 2 × p25). Achados: 30.238 serviços com código de material (R$ 13,7 bi);
  cabeçalho de contratação repetido duplicava 269 linhas na fato (pego pelo teste de totais);
  dry run não devolve bytes para tabelas particionadas por ano (o teto fica com
  `maximum_bytes_billed`).
  **Pendências (usuário):** (1) CI não disparou nos PRs — nenhum workflow registrado no
  repositório após abrir os PRs; investigar em Settings → Actions; (2) instalar Power BI
  Desktop, montar o painel por `powerbi/roteiro.md`, conferir a tabela de `medidas.md` (o DAX
  não foi executado), publicar; (3) merge dos PRs **em ordem, #1 primeiro, sempre o que tem
  base `main`**; (4) auditoria do histórico e tornar público; (5) pin no perfil e LinkedIn.
  Planos seguintes: padronização por IA, Agente Auditor, pipeline agendado/dbt.
