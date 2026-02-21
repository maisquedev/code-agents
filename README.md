# code-agents

Coleção de subagentes para Claude Code.

Desenvolvidos como material de apoio ao curso **Dev com IA**, usando Laravel/PHP como base — mas aplicáveis a qualquer stack.

## Agentes disponíveis

| Agente | Invocação | O que faz |
|---|---|---|
| `biblioteca` | `use o agente biblioteca` | Gerencia catálogo de livros via API REST, com busca automática de metadados na Open Library |
| `code-reviewer` | `use o agente code-reviewer` | Auditoria de código com checklist OWASP Top 10, severidade (CRITICAL/HIGH/MEDIUM/LOW) e relatório formatado |
| `migration-expert` | `use o agente migration-expert` | Cria e revisa migrations Laravel com boas práticas de schema, indexes e reversibilidade |

## Instalação

Clone este repositório dentro do seu projeto:

```bash
cd seu-projeto
git clone git@github-curso:maisquedev/code-agents.git .claude/agents
```

Ou como submódulo Git, para manter atualizações:

```bash
git submodule add git@github-curso:maisquedev/code-agents.git .claude/agents
```

## Estrutura

```
.claude/agents/
├── biblioteca.md
├── code-reviewer.md
├── code-reviewer-better.md
├── migration-expert.md
└── docs/
    ├── exemplo-biblioteca.md
    └── texto-para-CLAUDE.md
```

## Transparência de subagentes

O arquivo `docs/texto-para-CLAUDE.md` é um trecho para adicionar ao `CLAUDE.md` do seu projeto. Ele instrui o Claude a exibir um relatório sempre que subagentes forem utilizados, mostrando quais foram invocados, quais arquivos acessaram e se rodaram em paralelo.

Para ativar, copie o conteúdo do arquivo para o `CLAUDE.md` na raiz do seu projeto:

```bash
cat .claude/agents/docs/texto-para-CLAUDE.md >> CLAUDE.md
```

## Links úteis

**Subagentes no Claude Code**
- [Build with Claude Code — Subagents](https://docs.anthropic.com/pt/docs/claude-code/sub-agents)

**Frontmatter YAML**
- [YAML front matter — Assemble](https://assemble.io/docs/YAML-front-matter.html)
- [Front Matter — Jekyll](https://jekyllrb.com/docs/front-matter/)
- [Usar front matter YAML — GitHub Docs](https://docs.github.com/pt/contributing/writing-for-github-docs/using-yaml-frontmatter)

---

## Sobre este repositório

Este repositório é parte do curso **Dev com IA** — um curso prático para desenvolvedores que querem usar inteligência artificial no dia a dia do trabalho, de verdade, sem enrolação.

Você aprende a criar seus próprios agentes, automatizar tarefas repetitivas e escrever código melhor e mais rápido com a ajuda do Claude Code — usando Laravel como exemplo, mas com conceitos que funcionam em qualquer linguagem.

🔗 **[Saiba mais em dev-com-ia.sim10.com](https://dev-com-ia.sim10.com/)**

---

Licença MIT
