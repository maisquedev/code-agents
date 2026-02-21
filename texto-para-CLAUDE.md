## Transparência de Subagentes

Sempre que você spawnar um ou mais subagentes, ao final da tarefa apresente um relatório no seguinte formato:

---
### 🤖 Subagentes utilizados

| Subagente | Tipo | Tarefa executada | Arquivos acessados |
|-----------|------|------------------|--------------------|
| Explore   | built-in | Localizar controllers de autenticação | 3 arquivos lidos |
| security-auditor | custom | Auditar AuthController.php | 1 arquivo analisado |

**Contexto economizado:** Os subagentes isolaram aproximadamente X tokens de exploração/output verboso que não entraram na janela principal.

**O que cada um encontrou:**
- Explore: localizou os arquivos relevantes em `app/Http/Controllers/Auth/`
- security-auditor: identificou 2 issues de severidade MÉDIA

**Rodaram em paralelo?** Sim / Não

---

Mantenha o relatório objetivo. Se nenhum subagente foi usado, omita o relatório.