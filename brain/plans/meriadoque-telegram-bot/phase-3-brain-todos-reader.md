# Fase 3 — Leitor de Memória e Backlog

Back to [[meriadoque-telegram-bot/overview]]

## Objetivo

Implementar a classe `QGKnowledgeReader` em `core/brain_reader.py` para fazer a interface segura com o diretório do QG (`d:/AGENTE-MERIADOQUE`), lendo e estruturando tarefas do `todos.md`, pesquisando notas no `brain/` e permitindo a adição de novas ideias na seção de rascunhos.

## Mudanças

- `core/brain_reader.py`:
  - `parse_todos(qg_path: Path) -> dict[str, list[dict]]`:
    - Faz o parsing das seções do `todos.md` (Inbox, Pending, In Progress, Done).
    - Retorna lista estruturada de tarefas com status e descrição.
  - `add_inbox_idea(qg_path: Path, idea_text: str) -> bool`:
    - Adiciona uma nova linha com checkbox pendente `[ ]` na seção Inbox do `todos.md`.
    - Respeita o marcador `<!-- next-id: N -->` se existir, incrementando o contador.
  - `search_brain(qg_path: Path, query: str, limit: int = 3) -> list[dict]`:
    - Varre arquivos markdown em `brain/` (princípios, codebase, planos) procurando ocorrências das palavras-chave da busca.
    - Retorna título do arquivo, caminho relativo e trecho relevante para compor o contexto da IA.
- `tests/test_brain_reader.py`:
  - Testes com diretório temporário (`tmp_path`) criando arquivos fictícios de `todos.md` e `brain/`.
  - Validação de inserção atômica de ideias sem corromper o arquivo existente.
  - Validação de busca retornando os trechos corretos.

## Estrutura de Dados

```python
class TaskItem:
    id: int | None
    description: str
    status: str  # "pending", "in_progress", "done"

class BrainSearchResult:
    file_path: str
    title: str
    snippet: str
```

## Verificação

### Estática
- `ruff check core/brain_reader.py tests/test_brain_reader.py`
- `pytest tests/test_brain_reader.py`

### Runtime
- Rodar script manual ou teste de integração lendo o arquivo real `d:/AGENTE-MERIADOQUE/todos.md` e conferindo a lista de tarefas impressa no terminal.
