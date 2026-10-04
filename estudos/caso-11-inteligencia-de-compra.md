# Caso 11 — Inteligência de Compra: BigQuery, SQL analítico, Data Quality e modelo estrela

Data: 2026-10-03 · Repositório: `diaquinodev/inteligencia-de-compra` (privado até o painel ficar pronto)

## 1. Contexto

O portfólio só tinha projetos de automação e IA aplicada. Faltava evidência pública de
BigQuery, SQL analítico, modelagem dimensional e Power BI — a base de uma vaga de Analista de
Dados. No brainstorm, o tema "e-commerce com dataset público" foi descartado por ser genérico
e por repetir o nicho de marketplace. Ficou **análise de compras** (*spend analytics*): liga
com o Recebimento Fiscal (mesmo processo de compras) e com o alvo de empresas grandes.

## 2. O que fizemos (em ordem)

| Fase | Entrega | Prova |
|---|---|---|
| 0 | validar os dados antes de construir | 93% dos itens com código, 79% com preço; total de 2024 de R$ 6,1 trilhões na origem |
| 1 | executor de SQL com estimativa e teto de custo | 10 testes; ruff, mypy |
| 2 | camadas bruta e limpa | 426.237 → 334.901 linhas válidas; rodar de novo dá o mesmo |
| 3 | modelo estrela | fato = camada limpa em linhas e em R$ |
| 4 | três análises | números do README saem das tabelas `mart_` |
| 5 | testes de qualidade | 11 de 11; 5 falham com 3 erros plantados |
| 6 | painel do Power BI gerado por código | 23 de 23 valores conferem com o BigQuery; 14 testes novos |
| 7 | README com as imagens do painel | pendente: merge e publicação |

Comandos que valem decorar:

```
gcloud auth login
gcloud config set project inteligencia-de-compra
python -m compras construir            # recria as 18 tabelas
python -m compras construir --estimar  # só estima o custo
python -m compras testar               # 11 testes de qualidade
```

### Painel como código (2026-10-04)

O painel não foi montado clicando. Um script gera os arquivos do projeto do Power BI a partir
de três fontes em texto (medidas, páginas, tema); outro abre o projeto, carrega os dados e
compara os números com o BigQuery.

```
python -m compras painel        # gera o projeto (PBIP)
python -m compras esperado      # valores de referência, calculados no BigQuery
powerbi\scripts\abrir.ps1       # abre no Power BI e carrega os dados
powerbi\scripts\conferir.ps1    # compara painel x BigQuery: 23 de 23
powerbi\scripts\capturar.ps1    # imagem de cada página
```

O que os testes pegaram antes de qualquer pessoa ver o painel: uma medida que não terminava
de calcular, uma participação que daria 100% entre os "15 maiores" e um script que travava o
Power BI.

## 3. Conceitos

- **Spend analytics** — análise do gasto de uma organização: com quem, em quê e a que preço.
- **Compras indiretas** — o que a empresa compra e não vai no produto: escritório, TI, limpeza.
- **ELT** — extrair, carregar e só então transformar, dentro do banco, com SQL.
- **Camadas** — bruta (cópia do recorte), limpa (padronizada), modelo (estrela), análises.
  Analogia: receber a mercadoria, conferir, guardar na prateleira certa, montar o pedido.
- **Dry run** — pedir ao banco "quanto isso custaria?" sem executar.
- **Idempotente** — rodar duas vezes dá o mesmo resultado. Aqui: `CREATE OR REPLACE TABLE`.
- **CTE** — bloco nomeado com `WITH`; quebra a consulta em passos legíveis.
- **Window function** — calcula sobre um grupo sem juntar as linhas (`... OVER (PARTITION BY ...)`).
  O `GROUP BY` devolve uma linha por grupo; a window function mantém todas e acrescenta o
  valor do grupo em cada uma. Por isso dá para comparar cada compra com a mediana do seu item.
- **Percentil / quartil** — p25, mediana (p50) e p75 dividem as compras em quatro partes.
- **Modelo estrela** — uma tabela de fatos (medidas) no centro e dimensões (quem, o quê,
  quando) em volta. **Grão** é o que uma linha da fato representa.
- **Chave substituta** — número criado só para ligar fato e dimensão.
- **Curva ABC** — A: poucos fornecedores com 80% do gasto; C: muitos com 5%.
- **HHI** — soma dos quadrados das participações; mede concentração de mercado.
- **Teste de qualidade** — consulta que deve voltar vazia; cada linha devolvida é um erro.
- **PBIP / TMDL / PBIR** — o painel do Power BI salvo como pasta de arquivos de texto:
  TMDL descreve o modelo e as medidas; PBIR descreve páginas e visuais. Analogia: a planta
  da casa em papel, em vez da casa pronta; dá para revisar, copiar e reconstruir.
- **Painel como código** — gerar o painel por script e testar por programa, como se faz
  com qualquer software.
- **Conferência independente** — calcular o mesmo número por dois caminhos (DAX e SQL) e
  comparar. Se batem, os dois estão certos ou erram igual, o que é bem mais raro.

## 4. Por que assim

- **Validar antes de construir.** Uma consulta de cobertura decide se o projeto existe.
- **Trava de custo no código.** A cota gratuita é finita; a regra não pode depender de atenção.
- **Nenhuma linha some sem motivo.** A camada limpa guarda `motivo_exclusao`; o relatório
  mostra quanto ficou de fora. Quem lê a análise precisa saber o que não entrou.
- **SQL puro antes de dbt.** Entender primeiro o que a ferramenta automatiza.
- **Três estimativas de economia, decisão pela menor.** R$ 2.269,8 mi (teto), R$ 1.074,9 mi
  (conservadora) e R$ 263,7 mi (defensável). O número pequeno resiste a perguntas.
- **Dado público dito como dado público.** O README declara que são compras do governo no
  papel de um ERP. Escondido, viraria ponto fraco.

## 5. Hacks e pegadinhas

- **A estimativa de custo pode vir vazia.** Para tabelas particionadas, o BigQuery não
  informou os bytes. Tratar "sem estimativa" como zero desligaria a trava sem avisar.
- **Junção que duplica.** O cabeçalho da contratação se repete a cada republicação; a fato
  saiu com 269 linhas a mais. O teste que compara totais entre camadas pegou.
- **Código igual, coisa diferente.** 30.238 linhas de serviço usavam códigos que também
  existem no catálogo de materiais: R$ 13,7 bilhões que não eram do recorte.
- **Preço de lote no campo de preço unitário.** Um microcomputador por R$ 144,9 milhões.
  Regra: mais de 10 vezes fora da mediana sai da comparação, mas continua no gasto.
- **O mesmo código cobre produtos diferentes.** "Notebook até 14 pol." vai de R$ 3,4 mil a
  R$ 11,8 mil por configuração. Economia calculada aí é ilusão.
- **Unidade de medida suja.** 948 grafias; "Caixa 12,00 UN" e "CAIXA 12 UN" são a mesma.
- **Teste que nunca falha não prova nada.** Plantar erros de propósito e ver o teste acusar.
- **Windows:** o `bq` falha pelo Git Bash e o PowerShell injeta BOM no pipe.

## 6. O que estudar (prioridade)

1. Window functions: `ROW_NUMBER`, `RANK`, `SUM() OVER`, `PERCENTILE_CONT`, `QUALIFY`.
2. Modelagem dimensional: grão, fato, dimensão, chave substituta (Kimball).
3. DAX: contexto de filtro, `CALCULATE`, `ALLSELECTED`, `SUMX`, inteligência de tempo.
4. Data Quality: unicidade, integridade referencial, validade, reconciliação de totais.
5. BigQuery: particionamento, custo por bytes lidos, dry run.
6. Compras: curva ABC, preço de referência, compra centralizada.

## 7. Perguntas para o NotebookLM

1. Qual a diferença entre `GROUP BY` e uma window function? Dê um exemplo do projeto.
2. Por que a fato saiu com 269 linhas a mais e qual teste acusou o erro?
3. O que é o grão de uma tabela fato e por que defini-lo primeiro?
4. Por que há três estimativas de economia e qual delas sustenta a decisão?
5. Por que uma compra com preço suspeito continua no gasto total, mas sai da comparação?
6. O que acontece se "sem estimativa de custo" for tratado como custo zero?
7. Por que manter as linhas excluídas com o motivo, em vez de apagá-las?
8. O que um HHI de 197 diz sobre os fornecedores de informática?
9. Por que gerar o painel por script, se montar à mão é mais rápido da primeira vez?
10. Por que `ALLSELECTED` dá a participação errada num visual com filtro de "N maiores"?
11. O que torna a conferência dos números "independente", e por que isso importa?

## 8. Referências

- Base dos Dados — Licitações e Contratos do Governo Federal:
  https://basedosdados.org/dataset/bb11d3e6-6bac-412e-bbb8-773369771a70
- Google Cloud — BigQuery sandbox: https://docs.cloud.google.com/bigquery/docs/sandbox
- Google Cloud — instalar o gcloud CLI: https://cloud.google.com/sdk/docs/install
