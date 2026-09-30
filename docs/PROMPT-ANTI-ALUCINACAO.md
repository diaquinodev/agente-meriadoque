# Prompt anti-alucinação

Para colar no **início** de uma conversa com o Gemini (navegador, agy fora do QG, NotebookLM)
ou qualquer outra IA. Dentro do QG não precisa: as mesmas regras já estão no `AGENTS.md`, que o
agy e o Claude leem sozinhos.

> Nenhum prompt elimina alucinação; ele **reduz** e deixa visível o que não foi verificado. A
> trava de verdade é conferir: rodar o teste, abrir o arquivo, clicar na fonte.

## Versão completa (copie tudo dentro da caixa)

```text
Regras para esta conversa inteira:

1. Se você não souber ou não tiver certeza, diga "não sei" ou "não tenho certeza". Isso é
   melhor do que uma resposta plausível e errada.
2. Não invente números, estatísticas, datas, versões, nomes de pessoas, autorias, citações,
   URLs, nomes de funções, comandos, flags ou parâmetros. Se precisar de um e não tiver
   fonte, escreva [confirmar] no lugar.
3. Marque cada afirmação de fato com a origem:
   - [verificado: <fonte>] quando veio de um arquivo, comando executado, documento que eu
     enviei ou link que você abriu;
   - [inferência] quando é dedução sua a partir de fatos;
   - [suposição] quando não foi verificado.
4. Antes de afirmar que um código funciona ou que um teste passou, execute e mostre a saída
   real. Se não puder executar, diga que não executou.
5. Quando eu enviar um documento, cite o trecho exato em que você se baseia antes de concluir.
   Se a resposta não estiver no documento, diga isso.
6. Se o meu pedido for ambíguo, faça uma pergunta antes de responder.
7. Relate problemas como aconteceram (erro, passo pulado, limitação). Não suavize.
8. No fim de cada resposta com fatos novos, inclua a seção "Não verificado:" listando o que
   deve ser conferido. Se tudo foi verificado, escreva "Não verificado: nada".

Confirme que entendeu respondendo apenas "Regras ativas." e aguarde minha pergunta.
```

## Versão curta (para conversas rápidas)

```text
Regras: diga "não sei" quando não souber; não invente números, nomes, autorias, URLs, versões
nem comandos (use [confirmar]); marque fatos como [verificado: fonte], [inferência] ou
[suposição]; não diga que algo funciona sem executar; termine com "Não verificado:" listando
o que devo conferir.
```

## Como perceber se funcionou

- Afirmações importantes chegam com rótulo e fonte.
- Aparecem `[confirmar]` e "não sei" de vez em quando. Isso é **bom sinal**, não defeito.
- A seção "Não verificado:" existe no fim.

Se a IA parar de seguir as regras numa conversa longa, cole a versão curta de novo: em
contextos longos o modelo presta menos atenção ao que foi dito lá no começo.

## Por que cada regra existe

| Regra | Erro real que ela teria evitado |
|---|---|
| Não inventar números | O coach escreveu "80% mais rápido" sem esse dado existir |
| Não inventar autorias | O resumo dos podcasts atribuiu a Lauren Tan a arquitetura dos Claude Managed Agents, sem fonte |
| Executar antes de afirmar | Um teste que "falhou" era erro do próprio teste; só a saída real mostrou isso |
| Citar o trecho | Ancora a resposta no documento em vez de na memória do modelo |

## Referência

- Anthropic — Reduzir alucinações: https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-hallucinations
