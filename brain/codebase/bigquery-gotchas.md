# BigQuery gotchas (sandbox, Base dos Dados, Windows)

Aprendido no projeto inteligencia-de-compra (2026-10-03). Ver caso-11.

- **Sandbox sem cartão funciona com login de usuário** (`gcloud auth login`). Em Python, sem
  credencial padrão (ADC), dá para usar o token de `gcloud auth print-access-token`.
  Limites: 1 TiB/mês de consulta, 10 GiB, tabelas expiram em 60 dias, sem DML — tudo por
  `CREATE OR REPLACE TABLE ... AS SELECT`.
- **Dry run pode não devolver bytes.** Em tabelas particionadas por faixa de inteiro (ex.:
  `contratacao_item`, por `ano`) `total_bytes_processed` vem `None`. Não converter `None` em 0:
  isso desliga a trava em silêncio. O teto real é `maximum_bytes_billed`.
- **`INFORMATION_SCHEMA` de projeto público alheio é negado** (`basedosdados`). Usar
  `bq ls projeto:dataset` e `bq show --format=json`.
- **`bq.cmd` no Windows:** falha a partir do Git Bash (espaço em "Cloud SDK"); pelo PowerShell,
  o pipe injeta BOM e a consulta quebra. Usar `cmd /c "bq query ... < arquivo.sql"` ou o
  cliente Python.
- **Junção com tabela de cabeçalho duplica linhas** quando a origem repete o cabeçalho
  (republicações). Sempre ter um teste que compara contagem e soma da fato com a camada
  anterior; foi ele que acusou 269 linhas a mais.
- **Código de catálogo não é identidade de produto.** O mesmo código cobre especificações e
  embalagens diferentes; e códigos de serviço coincidem com códigos de material. Comparar
  preço só dentro de (item, unidade) e filtrar o tipo do item.
- **Número grande não é número defensável.** Estimativa de economia saiu em três níveis; só o
  de grupos de preço homogêneo (p75 ≤ 2 × p25) resiste a perguntas.
- **Heredoc longo no Bash da ferramenta falha** com "unexpected EOF" antes de executar
  qualquer linha. Para vários arquivos, gravar com a ferramenta de escrita ou com um script
  Python salvo em arquivo. Ver [[codebase/windows-toolchain-gotchas]].
- **GitHub Actions não disparou** em repositório privado recém-criado com PRs abertos em
  seguida (nenhum workflow registrado, nem após novo push). Causa não identificada; os passos
  do CI foram rodados num clone limpo como substituto.

Related: [[codebase/llm-output-guards]], [[principles/prove-it-works]]
