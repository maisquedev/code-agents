# code-agents

Repositório com exemplos de subagentes customizados para o **Claude Code CLI**. Cada agente é definido como um arquivo Markdown com um system prompt especializado e configurações de ferramentas.

## O que são subagentes no Claude Code?

Subagentes são especialistas que o Claude Code pode invocar para tarefas específicas. Cada um tem:

- Um **system prompt** focado em um domínio
- Um conjunto limitado de **ferramentas** (Bash, Read, Grep, etc.)
- Um **modelo** configurado (sonnet, haiku, opus)
- Um limite de turnos (`maxTurns`) para controlar o custo

O fluxo básico de um agente é: **raciocinar → executar ferramenta → observar resultado → raciocinar novamente**.

## Agentes disponíveis

### `biblioteca/`

Gerencia um catálogo de livros via API REST local (Laravel). Busca automaticamente metadados na [Open Library](https://openlibrary.org/) quando os dados estão incompletos.

**Ferramentas:** `Bash`, `WebFetch`

Operações suportadas:
- Listar, buscar, criar, atualizar e remover livros
- Validar ISBN, ano de publicação e editora
- Formatar respostas em tabelas Markdown

**Pré-requisito:** Servidor Laravel rodando em `http://localhost:8000`

---

### `code-reviewer/`

Revisa código em busca de problemas de segurança, qualidade e performance. Disponível em duas versões:

| Arquivo | Descrição |
|---------|-----------|
| `code-reviewer.md` | Versão simples com checklist básico |
| `code-reviewer-better.md` | Versão completa com OWASP Top 10, severidade (CRITICAL/HIGH/MEDIUM/LOW) e relatório formatado |

**Ferramentas:** `Read`, `Grep`, `Glob`, `Bash`

---

### `migration-expert/`

Cria e revisa migrations Laravel seguindo boas práticas: naming convention, indexes, foreign key constraints com `onDelete`/`onUpdate`, método `down()` reversível e separação de schema vs dados.

**Ferramentas:** `Read`, `Write`, `Bash`

---

## Como usar

Os arquivos `.md` de cada agente devem ser copiados para o diretório de agentes do Claude Code:

```bash
# Diretório padrão de agentes
~/.claude/agents/

# Exemplo
cp biblioteca/biblioteca.md ~/.claude/agents/biblioteca.md
```

Após isso, invoque o agente no Claude Code:

```
Use o subagente biblioteca para listar todos os livros cadastrados
```

```
Use o agente code-reviewer para analisar segurança em app/Controllers/AuthController.php
```

```
Use o migration-expert para criar uma migration para a tabela de comentários
```

## Estrutura do projeto

```
agents/
├── biblioteca/
│   └── biblioteca.md           # Agente gerenciador de catálogo de livros
├── code-reviewer/
│   ├── code-reviewer.md        # Code reviewer versão simples
│   └── code-reviewer-better.md # Code reviewer versão completa
├── migration-expert/
│   └── migration-expert.md     # Especialista em migrations Laravel
├── exemplo-biblioteca.md       # Exemplo detalhado de uso do agente biblioteca
└── texto-para-CLAUDE.md        # Guia de transparência ao usar múltiplos subagentes
```

## Formato de um agente

Todo agente começa com um frontmatter YAML seguido do system prompt:

```markdown
---
name: nome-do-agente
description: Quando e como este agente deve ser invocado
tools: Bash, Read, Grep
model: sonnet
maxTurns: 10
---

[System prompt do agente]
```
