# Fase 1 — Scaffold do repositório

Back to [[plans/auditor-de-repasses/overview]]

## Goal

Repositório novo com ferramentas de qualidade e CI funcionando antes de qualquer
feature (foundational-thinking: scaffold primeiro).

## Changes

- Criar `diaquinodev/auditor-de-repasses` (privado) com `main`.
- `pyproject.toml`: pacote `auditor` em `src/`, dependências, config de ruff, mypy
  (strict) e pytest.
- `.github/workflows/ci.yml`: lint, format check, mypy, pytest.
- `AGENTS.md` do projeto (regras do QG adaptadas + comandos de verificação), `CLAUDE.md`
  com `@AGENTS.md`, `.gitignore`, `.gitattributes`, `.coderabbit.yaml`.
- Um teste trivial para provar que a esteira roda.

## Data structures

Nenhuma.

## Verification

- Static: `ruff check .`, `ruff format --check .`, `mypy src`, `pytest` verdes localmente.
- Runtime: PR draft aberto → CI verde no GitHub. Quebrar um teste de propósito numa
  branch descartável e ver o CI ficar vermelho (prova que a esteira pega erro).
