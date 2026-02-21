# Plano: Subagente `biblioteca` para o Claude Code CLI

## Contexto

O projeto tem uma API REST de livros (`/api/livros`) com CRUD completo. O objetivo é criar um
**subagente customizado dentro do Claude Code CLI** — do mesmo tipo que os agentes `Explore`,
`Plan`, `Bash` já existentes — que use esta API (e a Open Library como API externa) para gerenciar
o catálogo de livros.

Este subagente serve como material **didático**: ao rodá-lo, o usuário vê ao vivo o loop
`raciocinar → usar ferramenta → observar resultado → raciocinar` que define um agente de IA.

---

## Como Subagentes Funcionam no Claude Code

Subagentes são definidos como **arquivos Markdown com frontmatter YAML** em `.claude/agents/`.
Cada arquivo define:
- `name` → identificador usado no `subagent_type` da Task tool
- `description` → quando o Claude deve delegar para este agente (automático ou manual)
- `tools` → lista de ferramentas permitidas (restrição de escopo)
- `model` → qual modelo usar
- `maxTurns` → limite de iterações do loop

O corpo do arquivo (abaixo do frontmatter) é o **system prompt** do agente.

---

## O Loop do Agente (demonstrado ao vivo)

Quando o subagente `biblioteca` recebe uma tarefa, o usuário vê no terminal:

```
▶ Task(biblioteca): "Adicione o livro Duna ao catálogo"

  → Agente pensa: preciso buscar os dados na Open Library primeiro

  [WebFetch] https://openlibrary.org/search.json?q=Duna+Frank+Herbert&...
  → Resultado: { titulo: "Dune", autor: "Frank Herbert", isbn: "9780441013593", ... }

  [Bash] curl -X POST http://localhost:8000/api/livros -d '{...}'
  → Resultado: { id: 1, titulo: "Duna", autor: "Frank Herbert", ... }

  Resposta final: "Livro 'Duna' adicionado com sucesso! (ID: 1)"
```

Cada chamada de ferramenta é o agente **agindo** após **raciocinar** sobre o que fazer.
Os resultados são **observados** e injetados de volta no contexto antes do próximo passo.

---

## Arquivo a Criar

### `.claude/agents/biblioteca.md`

É o **único arquivo necessário**. Nenhum código PHP, nenhuma dependência nova.

```markdown
---
name: biblioteca
description: >
  Gerencia o catálogo de livros via API REST local (localhost:8000/api/livros).
  Use quando o usuário pedir para listar, buscar, adicionar, atualizar ou excluir
  livros. Busca metadados automaticamente na Open Library quando necessário.
tools: Bash, WebFetch
model: sonnet
maxTurns: 15
---

[system prompt completo — ver seção abaixo]
```

---

## System Prompt do Agente

O system prompt (corpo do arquivo) ensina o agente:

### Seção 1 — Papel e ferramentas disponíveis
- É um assistente de biblioteca com duas fontes de dados:
  - API local (`http://localhost:8000/api/livros`) via `Bash` (curl)
  - Open Library (`https://openlibrary.org/search.json`) via `WebFetch`

### Seção 2 — Receitas de curl para cada operação
Exemplos exatos de como chamar cada endpoint (GET, POST, PUT, DELETE) com os headers corretos.

### Seção 3 — Schema dos campos
Os campos obrigatórios e opcionais do livro, com tipos e validações.

### Seção 4 — Estratégia de trabalho
Regras de comportamento:
1. Se o usuário pede para adicionar um livro sem fornecer ISBN/editora → buscar na Open Library primeiro
2. Sempre confirmar o resultado de cada operação com os dados retornados
3. Em caso de erro 422, explicar a validação que falhou
4. Ao listar, formatar como tabela markdown

---

## Por Que Esta Abordagem É Didática

| Conceito | Onde aparece |
|---|---|
| **Loop do agente** | Claude itera automaticamente entre raciocinar e agir |
| **Tool use** | `Bash` (curl) e `WebFetch` usados conforme a necessidade |
| **System prompt** | O arquivo `.md` é o "cérebro" do agente — lido e editável |
| **Restrição de escopo** | `tools: Bash, WebFetch` — o agente não pode editar arquivos |
| **API externa** | Open Library enriquece dados sem autenticação |
| **Delegação** | `Task(biblioteca, "...")` mostra como um agente delega para outro |
| **Descrição semântica** | `description` ensina quando delegar automaticamente |

---

## Como Invocar o Subagente

**Forma explícita** (dentro de uma conversa com Claude Code):
> "Use o subagente `biblioteca` para listar todos os livros cadastrados"

**Via Task tool em outro agente ou skill:**
```
Task(biblioteca): "Adicione Harry Potter e busque os dados automaticamente"
```

**Automático:** O Claude delega sozinho quando a `description` bate com a solicitação do usuário.

---

## Arquivo a Criar

```
.claude/
  agents/
    biblioteca.md    ← NOVO (único arquivo necessário)
```

---

## Pré-requisitos

O servidor Laravel deve estar rodando para a API local funcionar:
```bash
php artisan serve   # http://localhost:8000
```

A Open Library é pública e não precisa de chave de API.

---

## Verificação

```bash
# 1. Subir o servidor
php artisan serve

# 2. Na conversa do Claude Code, invocar o agente:
# "Use o subagente biblioteca para listar os livros"
# "Peça ao agente biblioteca para adicionar o livro 1984"
# "Via subagente biblioteca, exclua o livro com ID 2"
```
