# Caso 09 — Recebimento Fiscal: 3-way match, SOAP, OCR e um agente com travas

Data: 2026-10-01 · Repositório: `diaquinodev/recebimento-fiscal`

## 1. Contexto

Objetivo: um case de portfólio para **empresas grandes** (não nicho de marketplace) que
fechasse lacunas de tecnologia: function calling, OCR/IDP, SOAP/XML, banco relacional
(Oracle). A pesquisa levou ao processo de **recebimento fiscal** de quem usa ERP corporativo:
o *3-way match* entre pedido de compra (SAP `ME21N`), recebimento (`MIGO`) e NF-e (`MIRO`).

## 2. O que fizemos (em ordem)

| Fase | Entrega | Prova |
|---|---|---|
| Brainstorm + pesquisa | dor real (pagamento retido por divergência; SAP GRC descontinuado) | fontes no brief |
| 1 | domínio, NF-e XML 4.00, dígito verificador, gerador com gabarito | 21 testes |
| 2 | banco (SQLAlchemy), motor 3-way match, eval | 480 notas, 100% detecção, 0 falso bloqueio |
| 3 | SOAP com WSDL, SEFAZ simulado, API | envelope do cliente `zeep` aceito |
| 4 | OCR de DANFE "fotografada" com Gemini + conferência em código | 22/22 campos fiscais (prompt v2) |
| 5 | agente com function calling e travas de negócio | 12/12 pareceres válidos |
| 6 | tela Streamlit com aprovação humana, README | AppTest de ponta a ponta |
| 7 | publicação: repo público, banco por visitante, deploy no Streamlit Cloud | teste com 2 visitantes falhou antes e passou depois |

### Publicação (2026-10-02) — o merge que deu errado e como consertar

- **Auditoria antes de abrir o repositório:** busca de chaves em todas as versões do
  histórico, arquivos `.env`/`.pem`/`.db` e arquivos grandes. Nada encontrado.
- **Link público muda o desenho:** `st.cache_resource` guarda um objeto para o servidor
  inteiro. Na máquina de uma pessoa isso não aparece; num link público, a aprovação de um
  visitante apareceria para todos. Estado de cada pessoa vai em `st.session_state`.
- **PRs empilhados:** cada fase nasce da anterior. Três erros juntos travaram a pilha: o
  primeiro merge foi *squash* (o git deixa de reconhecer os commits), três PRs foram
  mergeados antes do anterior (caíram em branches intermediárias) e os restantes ficaram em
  conflito. O conserto não perdeu nada porque a última branch tinha o projeto inteiro:
  `git merge -s ours` registra a `main` como incorporada sem mudar nenhum arquivo.
- **Regra para decorar:** só mergeie o PR cuja base é `main`; leia o texto do botão verde.

## 3. Conceitos

- **3-way match** — conferência de três documentos antes de pagar; divergência fora da
  tolerância bloqueia o pagamento.
- **NF-e / DANFE** — a NF-e é o XML assinado (o documento fiscal); a DANFE é só a versão
  impressa. Por isso OCR serve para triagem, não substitui o XML.
- **Chave de acesso** — 44 dígitos que repetem UF, mês, CNPJ, série e número, mais um dígito
  verificador (módulo 11). Redundância permite conferir e até reconstruir a chave.
- **SOAP / WSDL** — integração em XML com contrato formal; ainda é o padrão em governo e ERP.
- **XXE** — ataque com XML que lê arquivos do servidor; evitado com parser sem entidades.
- **Function calling** — a IA pede para o sistema executar funções e decide o próximo passo.
- **Menor privilégio** — a ferramenta do agente só enxerga a nota em análise; quem fixa é o
  código, não a IA.
- **Gravação e reprodução** — respostas reais da IA salvas em disco; testes e CI rodam sem
  chave, sem custo e sempre iguais.

## 4. Por que assim

- **IA onde há texto e imagem; código onde há regra e dinheiro.** O motor de match é uma
  função pura; a IA só lê a DANFE, investiga e redige. Toda decisão financeira é auditável.
- **SQLite agora, Oracle depois.** O Docker não estava instalado; a camada usa tipos
  portáveis (SQLAlchemy `Numeric`) para trocar só a URL. O README diz que Oracle ainda não
  foi executado — afirmar o contrário seria inventar.
- **Eval antes de confiar.** Cada parte com IA teve um eval com números; as versões ruins
  ficaram registradas no PR e no README, porque mostram o processo.

## 5. Hacks e pegadinhas

- **Uma amostra mente.** A primeira leitura de DANFE saiu perfeita; o eval com 22 mostrou que
  9 chaves vinham erradas. Todas foram barradas pelo dígito verificador.
- **Modelos de visão erram sequências longas de dígitos repetidos** ("2020 2020"). Pedir a
  chave **em grupos de 4, como impressa**, levou o acerto de 13/22 para 22/22.
- **Trava que só olha o campo estruturado tem brecha:** a ação era válida, mas o **e-mail**
  sugeria carta de correção para preço (proibido). A trava passou a ler o texto.
- **Trava burra barra resposta certa:** "não pode ser corrigida por CC-e" também menciona
  CC-e. A regra passou a ignorar frases com negação ("não", "impossibilitando"…). Barrar o
  certo é tão ruim quanto deixar passar o errado — o eval mostrou: 6/12 → 12/12.
- **SQLite em memória + API com threads:** cada thread abria um banco vazio. Solução:
  `StaticPool` (uma conexão compartilhada).
- **Streamlit lê `$…$` como fórmula:** "R$ 306,34 … R$ 289,00" virou texto quebrado. Escapar
  o cifrão em todo texto que vai para Markdown.
- **`texto.replace("", novo)`** insere `novo` entre todos os caracteres: um padrão que não
  existia gerou um arquivo de 11 MB. Recuperado pelo git.

## 6. O que estudar (prioridade)

1. Processo P2P em ERP: pedido, recebimento, verificação de fatura, tolerâncias, bloqueio.
2. NF-e: estrutura do XML, chave de acesso, CFOP, ICMS/IPI, carta de correção × cancelamento.
3. Function calling e desenho de agentes com travas (guardrails) em código.
4. Eval de sistemas com IA: métrica por tipo de erro, "erro aceito" × "erro detectado".
5. SOAP/WSDL e segurança de XML (XXE).

## 7. Perguntas para o NotebookLM

1. Por que o 3-way match bloqueia o pagamento em vez de rejeitar a nota quando o preço diverge?
2. Qual a diferença entre NF-e e DANFE, e por que isso limita o uso de OCR?
3. Como o dígito verificador da chave ajudou a impedir que leituras erradas entrassem?
4. Por que a carta de correção não pode ser usada para corrigir preço ou quantidade?
5. O que é "menor privilégio" no desenho das ferramentas de um agente?
6. Por que uma trava que barra respostas corretas é um problema tão sério quanto uma que
   deixa passar respostas erradas?
7. Para que serve gravar e reproduzir respostas reais da IA nos testes?

## 8. Referências

- Kamino — 3-way matching: https://kamino.com.br/blog/3-way-matching/
- Midas — descontinuação do SAP NF-e GRC:
  https://midassolutions.com.br/blog/nf-e-grc-descontinuado-conheca-alternativas/
- Fiscal.io — o que a CC-e pode e não pode corrigir:
  https://conteudo.fiscal.io/carta-de-correcao-eletronica/
- Manual SEFAZ-PR — serviço NFeConsultaProtocolo:
  http://moc.sped.fazenda.pr.gov.br/NFeConsultaProtocolo.html
- OWASP — XML External Entity (XXE): https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing
