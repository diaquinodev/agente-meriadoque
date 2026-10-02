# Armadilhas ao editar código por script

Custaram tempo três vezes em 2026-10-01 (caso 09). Complementa
[[codebase/windows-toolchain-gotchas]].

- **`s.replace("", novo)` insere `novo` entre todos os caracteres.** Um `s.index()` que
  casou com outra ocorrência ("return rel" dentro de "return relatorio") gerou fatia vazia e
  um arquivo de 11 MB. Antes de `replace` com trecho calculado: `assert trecho` e
  `assert s.count(trecho) == 1`.
- **Heredoc com Python não-raw corrompe escapes:** `"\b"` vira backspace (0x08), `"\$"` é
  escape inválido. Para regex e cifrão, usar a ferramenta Edit/Write ou strings `r"..."`.
  Conferir com `open(f, "rb").read().count(b"\x08")`.
- **Linha com caractere invisível não casa no Edit:** apagar pelo número (`sed -i 'Nd'`) e
  reescrever com o Edit.
- **Prettier/ruff reformatam após cada escrita:** reler antes do próximo Edit.
- **SQLite `:memory:` + servidor com threads:** cada conexão é um banco novo e vazio. Usar
  `StaticPool` e `check_same_thread=False`.
- **Streamlit Markdown lê `$…$` como fórmula:** escapar `$` (`r"\$"`) em todo texto de IA ou
  com "R$" exibido por `st.write`/`st.markdown`/`st.table`.
- **Streamlit `@st.cache_resource` é um só para o servidor inteiro:** num link público,
  estado guardado ali (banco em memória, decisões) aparece para todos os visitantes. Estado
  por visitante vai em `st.session_state`; teste com dois `AppTest` seguidos (caso 09, fase 7).
- **Pipeline de shell:** `grep ... | head; echo $?` mostra o status do `head`, não do
  `grep`. Para auditoria, rodar o `grep` sozinho.
