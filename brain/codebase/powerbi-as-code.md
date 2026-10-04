# Power BI como código (PBIP, TMDL, PBIR)

Aprendido no inteligencia-de-compra (2026-10-04). Fluxo e scripts em `powerbi/` desse repo.

- **Dá para gerar um painel inteiro por script.** Projeto PBIP = `X.pbip` + `X.SemanticModel/`
  (TMDL) + `X.Report/` (PBIR: um `visual.json` por visual). O Power BI Desktop 2.158 abriu um
  projeto escrito do zero, sem "Salvar como".
- **O motor local é a porta de entrada.** Cada janela do Desktop sobe um `msmdsrv.exe` filho;
  a porta está em `msmdsrv.port.txt` na pasta de trabalho (linha de comando `-s`). Com as DLLs
  do NuGet (`Microsoft.AnalysisServices.retail.amd64` e `...AdomdClient.retail.amd64`, pasta
  `lib/net45`, carregam no PowerShell 5.1) dá para: ler o modelo, exportar TMDL
  (`TmdlSerializer.SerializeDatabaseToFolder`), validar TMDL sem abrir o Desktop
  (`DeserializeDatabaseFromFolder`), rodar DAX e pedir refresh (`RequestRefresh` + `SaveChanges`).
  A pasta `bin` do Desktop não traz as DLLs cliente; não há provedor MSOLAP instalado.
- **Refresh cedo demais trava o Desktop** (carga segura o modelo que a abertura ainda precisa).
  Esperar a aba da página aparecer via UI Automation antes de pedir a carga.
- **PBIP aberto sem cache não tem dados:** medidas saem em branco até o refresh (~90 s aqui).
  Não salvar pelo Desktop quando a fonte é o gerador: ele regrava os arquivos.
- **Tabela só de medidas em TMDL:** partição M `#table(type table [Coluna = text], {})` e uma
  coluna oculta. Expressão multilinha: linhas recuadas um nível além das propriedades.
  `formatString: \\R\\$ #,0` funciona sem aspas.
- **Esquemas PBIR** estão em `microsoft/json-schemas` (MIT), pasta `fabric/item/report`.
  Versões antigas (2025) abrem no Desktop novo. Tema custom: `RegisteredResources` +
  `themeCollection.customTheme` com `reportVersionAtImport` texto qualquer ("5.59").
- **Filtro "N maiores"** em `filterConfig`: tipo `TopN`, com `Subquery` (`Top`, `OrderBy`
  direção 2) e `Where ... In ... Table`. Largura de coluna de tabela: objeto `columnWidth` com
  `selector.metadata` = queryRef.
- **`ALLSELECTED` + filtro TopN no visual** calcula a participação só entre os N mostrados.
  Usar `REMOVEFILTERS(dim)`. **Acumulado por ranking** escrito com medida dentro do `FILTER`
  é O(n²) e não termina com ~7 mil linhas: materializar com `ADDCOLUMNS` numa variável.
- **Ver sem controlar a tela:** `PrintWindow` (flag 2) captura só a janela; UI Automation
  (`SelectionItemPattern` no `TabItem`) troca de página. A árvore de acessibilidade do WebView
  demora na primeira consulta.
- **`.ps1` sem BOM no PowerShell 5.1:** acento em literal quebra; manter os scripts em ASCII
  e ler arquivos com `ReadAllText(..., UTF8)`.
- **Palavra-chave em comentário quebra parser ingênuo:** cortar o `.dax` em `IndexOf("EVALUATE")`
  falhou quando um comentário passou a citar EVALUATE; usar `(?m)^EVALUATE`.

Related: [[codebase/bigquery-gotchas]], [[principles/prove-it-works]],
[[principles/encode-lessons-in-structure]]
