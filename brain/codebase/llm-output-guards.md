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

