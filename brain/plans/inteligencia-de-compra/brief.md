# Inteligência de Compra (Spend Analytics com dados públicos)

Data do brainstorm: 2026-10-03 · Repositório previsto: `diaquinodev/inteligencia-de-compra`

- **Problema:** empresa grande compra o mesmo item por preços muito diferentes conforme a
  unidade, o comprador e o fornecedor, e não enxerga isso porque os dados ficam espalhados no
  ERP. Compras/Controladoria precisa saber: (1) onde está o gasto, (2) onde se paga caro,
  (3) quanto dá para economizar renegociando os piores casos.
- **Users:** analista/gestor de Compras ou Controladoria (leitor do painel); recrutador técnico
  de Dados (leitor do README). Projeto de portfólio para a trilha Analista de Dados, fechando a
  lacuna de evidência pública em BigQuery, SQL analítico, modelagem dimensional e Power BI.
- **MVP scope (in):**
  - Fase 0 — validar os dados: itens têm código de catálogo, preço unitário e quantidade
    comparáveis? Se não, cai para o plano reserva (ver "Rejected").
  - Fase 1 — recorte de **uma categoria e 2–3 anos**; camadas bruto → limpo → analítico em
    BigQuery; SQL com CTEs e window functions (mediana por item, ranking de fornecedores,
    variação no tempo); testes de Data Quality (CNPJ válido, preço > 0, duplicatas, unidade de
    medida); modelo estrela (fato itens comprados; dimensões fornecedor, item, órgão, tempo);
    painel Power BI de 3 páginas com DAX (uma por pergunta do problema); README com achados e
    a decisão ("renegociar estes N itens economizaria R$ X").
- **Out of scope (later):**
  - Fase 2 — IA para padronizar descrições de itens sem código, com gabarito (~200 itens
    rotulados à mão) e taxa de acerto publicada; baixa confiança fica fora da comparação.
  - Fase 3 — Agente Auditor: investiga cada caso suspeito com ferramentas somente-leitura,
    escreve parecer com evidências, humano aprova.
  - Fase 4 — pipeline agendado (GitHub Actions) e migração para dbt.
  - Fora de vez: previsão de preços, alertas em tempo real, Brasil inteiro.
- **Chosen approach and why:** caminho B (compras/P2P). Liga com o Recebimento Fiscal (mesmo
  processo, agora pelo lado analítico) e com o alvo de empresas grandes; dado real e sujo
  permite Data Quality de verdade. IA entra por cima de um MVP que já se sustenta sozinho:
  IA onde há texto, SQL onde há regra e dinheiro. O README declara que são compras do
  governo usadas como substituto de um ERP corporativo.
- **Rejected alternatives and why:**
  - E-commerce clássico (`thelook_ecommerce`): rápido, mas genérico e dado limpo demais.
  - Olist com olhar de operações: dataset muito usado; fica como **plano reserva** se a Fase 0
    falhar.
  - "Converse com o painel" (texto → SQL), resumo por IA, IA para achar outliers: enfeite ou
    pior que estatística simples em SQL.
  - dbt desde o início: mais uma ferramenta para aprender antes de entender o que ela automatiza.
- **Stack:** BigQuery (sandbox gratuito) sobre dados da Base dos Dados ("Licitações e Contratos
  do Governo Federal", CGU, 2013–jan/2026; tabelas de item com CNPJ do fornecedor
  [confirmar nomes de tabelas e colunas na Fase 0]); SQL puro em arquivos; Python só como
  executor (cliente `google-cloud-bigquery`) e testes; Power BI Desktop + DAX, publicado com
  conta de estudante. Fases 2–3: LLM via API com respostas gravadas em disco.
- **Verification (lint/types/tests):** lint de SQL (`sqlfluff`); testes de Data Quality como
  consultas que devem retornar zero linhas; `pytest` para o executor; pipeline reproduzível
  do zero por um comando (as tabelas do sandbox expiram em 60 dias); CI no GitHub Actions.
  Fases de IA: eval contra gabarito, com números no README.
- **Success criteria:** (1) um comando recria todas as tabelas e os testes passam; (2) o painel
  responde às 3 perguntas e está publicado ou, se bloqueado, em `.pbix` + prints + vídeo;
  (3) o README chega a uma decisão com valor em R$ e diz o que o dado não permite concluir;
  (4) o usuário explica cada consulta e cada medida DAX sem ajuda.
- **Open questions:**
  - A tabela de itens tem preço unitário, quantidade e código de catálogo com cobertura
    suficiente? (Fase 0 decide.)
  - Qual categoria e quais anos recortar (cabem em 10 GiB de armazenamento e 1 TiB/mês)?
  - A conta de estudante permite "publicar na web" no Power BI?
  - Sandbox sem DML: `CREATE TABLE AS SELECT` basta para todas as camadas?
  - Pré-requisitos ainda ausentes na máquina: projeto no Google Cloud, cliente BigQuery,
    Power BI Desktop.

Fontes: https://basedosdados.org/dataset/bb11d3e6-6bac-412e-bbb8-773369771a70 ·
https://docs.cloud.google.com/bigquery/docs/sandbox
