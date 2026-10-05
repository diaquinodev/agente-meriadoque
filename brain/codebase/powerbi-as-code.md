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

- **Design do painel (2026-10-04):** carregar a skill `dataviz` antes de escolher cores e rodar
  o validador de paleta. Com gráficos de uma série, uma cor só; verde e âmbar são cores de
  estado e a dupla ficou abaixo do alvo de daltonismo (6,8 < 8). Um número em destaque por
  página. Nove valores de escalas diferentes leem melhor em tabela que em colunas agrupadas.
  O que os testes não pegam e só a captura mostra: texto cortado em caixa de texto (margem
  interna do tema), filtro cortado, rótulo dentro da barra sem contraste. No tema do Power
  BI: `labels.labelPosition = "OutsideEnd"`, `categoryAxis.innerPadding`, eixos e grade por
  tipo de visual em `visualStyles`.

- **"Uma cor só" vale para os dados, não para a página (2026-10-04).** O painel com uma cor
  para tudo foi recusado pelo usuário como genérico. Duas camadas: cor de identidade da área
  na estrutura (faixa do cabeçalho, cartão principal, cabeçalho de tabela, 5 a 10% nos
  neutros) e cor de dado só nos gráficos. Hierarquia por tamanho de fonte com degraus de 20%
  ou mais. Cores de dado pouco saturadas reprovam no validador ("lê como cinza").
- **Layout de celular em PBIR:** arquivo `mobile.json` ao lado do `visual.json`, esquema
  `visualContainerMobileState`, com `position` (tela de 320 de largura) e, opcionalmente,
  `objects` / `visualContainerObjects` só para o celular. Funcionou sobrescrever tamanho de
  fonte de cartão, parágrafos de caixa de texto e `titleWrap` do título. Visual sem
  `mobile.json` não aparece no celular. Coluna de tabela não dá para esconder por layout.
- **Ver o layout de celular por script:** botões "Layout móvel" e "Layout da área de
  trabalho" via `InvokePattern`. Para rolar a tela do celular, `ScrollPattern` não alcança;
  `ScrollItemPattern.ScrollIntoView()` no elemento com o título do visual funciona.
- **Recorte da captura:** achar a página pela cor de fundo falha (a mesma cor aparece fora
  da página). Usar um visual de cor única e posição conhecida (a faixa do cabeçalho) para
  tirar escala e canto. A dica da aba selecionada fica por cima da margem inferior.
- **Rótulo de cartão corta sem avisar** quando passa da largura; no celular, em cartões em
  par (148 de largura), cabem cerca de 16 a 19 letras a 9 pt.

- **Entrega visual: perguntar antes, não decidir sozinho (2026-10-04).** A fase 8 foi
  desenhada sem briefing e refeita na fase 9. Antes de desenhar um painel, levantar: área
  de negócio, quem lê, tom (sóbrio ou vibrante), referência visual e se precisa de celular.
  Mostrar a primeira captura cedo, antes de polir e documentar.

- **Captura do celular, armadilhas (2026-10-05):** recorte de altura fixa corta cartão ao
  meio; cortar no fim do último visual que cabe inteiro (posições do `mobile.json`). A tela
  de celular pode abrir **rolada para baixo**: rolar até o primeiro texto da faixa com
  `ScrollItemPattern` antes de capturar. Com a aba Exibição ativa há **dois botões "Layout
  móvel"** (faixa de opções, só `TogglePattern`; barra de status, `InvokePattern`): filtrar
  pelo padrão aceito. Botão desabilitado = a janela já está nesse layout.
- **Conferir o número na imagem, não só o recorte.** Uma captura saiu com R$ 537 Mi no
  lugar de R$ 16 Bi: havia uma seleção ativa num visual da janela aberta (causa não
  confirmada; possivelmente um clique na janela). Reabrir o projeto antes de capturar
  imagem que vai para o README.

Related: [[codebase/bigquery-gotchas]], [[principles/prove-it-works]],
[[principles/encode-lessons-in-structure]]
