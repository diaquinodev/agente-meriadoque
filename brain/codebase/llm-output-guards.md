# Guardas para saída de LLM

Aprendido no Auditor de Repasses e no Coach de Entrevista. Relaciona com
[[principles/boundary-discipline]] e [[principles/prove-it-works]].

- **Prompt não impede número inventado.** O coach escreveu "80% mais rápido" mesmo com
  o prompt proibindo. Causa raiz: a busca mandava só 3 trechos do currículo e o dado real
  estava no 4º. Correção em duas camadas: mais contexto (5 trechos) **e** checagem em
  código que pede reescrita se aparecer número ausente da fala/currículo. Ver caso-05.
- **Conta é código, julgamento é IA.** Taxas, prazos, contagem de vícios e duração são
  calculados em código; a IA só extrai texto e redige.
- **Medir antes de confiar num campo da API.** `no_speech_prob` da Groq marcou ruído puro
  como fala — não serve de filtro. Ver caso-05.
- **Sem chave, ser explícito:** o modo demo/offline prova o pipeline, não a IA. Eval
  com provedor falso mede o código; dizer isso no README e no relatório final.
- **Agrupar antes de chamar a IA:** 80 divergências → 5 grupos por "assinatura do erro"
  = 5 chamadas, não 80. Economiza cota da API gratuita e é mais realista.
- **Gate humano depois da extração:** erro na extração do contrato contamina tudo;
  mostrar as regras extraídas para o usuário confirmar antes de conciliar.
- **Chamada ao vivo não tolera cold-start nem intervenção manual:** em copiloto de entrevista,
  o usuário não pode gerenciar opções no meio da conversa. A resiliência precisa ser autônoma:
  (1) *Pre-flight warmup* no boot aquece a conexão TLS e mede latência real com ping de 5 tokens;
  (2) *Auto-fallback com timeout:* se o modelo primário demorar >3.8s, comuta sozinho para modelo
  reserva (`gpt-4o-mini`), entregando resposta sem travar a tela.
- **Proteção contra socket HTTP pendurado (hung socket) em duas camadas:** `fetch` nativo no
  Node/Electron não possui timeout padrão. Se a nuvem do LLM mantiver a conexão aberta sem enviar dados,
  o processo fica pendurado por até 240s (timeout TCP do SO). Solução em duas camadas:
  (1) `AbortSignal.timeout(ms)` no cliente HTTP do backend com retry/fallback;
  (2) `Promise.race` com timeout máximo (ex: 10s) na interface para garantir que a UI nunca trave.


Aprendido no Recebimento Fiscal (caso 09):

- **Uma amostra mente; eval com N casos.** 1ª leitura de DANFE perfeita; eval com 22 mostrou
  9 chaves erradas (todas barradas pelo dígito verificador). Sempre medir antes de afirmar.
- **Visão erra sequências longas e repetitivas de dígitos.** Pedir "11 grupos de 4, como
  impressos" levou de 13/22 para 22/22. Checksum/redundância em código continua obrigatório.
- **Trava sobre campo estruturado tem brecha no texto livre:** ação válida, mas o e-mail
  sugeria o proibido. Validar também o texto que vai para fora.
- **Trava que barra a resposta certa é bug:** detector de "CC-e" pegava frases com negação.
  Medir falso positivo da trava no eval (6/12 → 12/12 após tratar negação).
- **Menor privilégio nas ferramentas:** o ID do objeto analisado é fixado pelo código, não
  vem do argumento da IA.
- **Separar erro de formato de erro de negócio:** conclusão em texto livre → uma chamada só
  de formatação (modo JSON), contada à parte.
- **Gravação/reprodução com id explícito** quando a mensagem tem imagem (bytes variam entre
  sistemas); CI roda evals de IA sem chave.
- **O prazo vale para a soma das tentativas, não para cada uma** (complementa o item do
  socket pendurado acima). Timeout por tentativa × nº de tentativas + backoff = espera real:
  `ia.py` do Recebimento Fiscal (120 s × 3 + pausas) pode prender a tela por mais de 6 min.
  Definir um deadline único por operação. Pendência em `todos.md`.

