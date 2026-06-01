# Guia de Contribuição

Convenções e boas práticas adotadas em todos os repositórios desta organização.

---

## Nomenclatura de repositórios

Formato: `{domínio}-{descritor}`

| Domínio | Uso |
|---------|-----|
| `data`  | Pipelines, ETL, ingestão, modelos dbt |
| `api`   | Serviços HTTP com contrato externo; tools de IA expostas como endpoint |
| `worker`| Jobs background, sync, automações; skills de IA executadas como processo |
| `infra` | IaC, Terraform, configuração de ambiente e CI/CD |
| `lib`   | Código reutilizável; tools de IA importadas como biblioteca por outros repos |
| `app`   | Interfaces, frontends, dashboards |
| `agent` | Sistemas autônomos com objetivo, memória e orquestração de tools/skills |

> **Onde entram tools e skills de IA?**
>
> | Formato | Domínio |
> |---------|---------|
> | Tool como função importada por outros repos | `lib` |
> | Tool exposta como endpoint HTTP | `api` |
> | Skill executada como job ou processo agendado | `worker` |
> | Sistema que orquestra tools/skills com autonomia | `agent` |
>
> **Dúvida entre `worker` e `agent`?**
> Se executa uma sequência fixa de passos → `worker`.
> Se observa, decide o que fazer e quando → `agent`.

**Exemplos por domínio**

```
# data
data-workflow-financial
data-pipeline-financial
data-ingestion-financial
data-dbt-models
data-scraper-financial

# api
api-commercial
api-auth
api-reports
api-webhooks-monday

# worker (jobs, sync e skills de IA como processo)
worker-monday-sync
worker-invoice-generator
worker-email-notifications
worker-slack-alerts
worker-data-quality-checks
worker-skill-report-gen
worker-skill-data-sync

# infra
infra-gcp-setup
infra-terraform-modules
infra-github-actions
infra-monitoring

# lib (utilitários e tools de IA reutilizáveis)
lib-python-utils
lib-bigquery-helpers
lib-monday-client
lib-auth-client
lib-tools-bigquery
lib-tools-monday

# app
app-dashboard-ops
app-portal-cliente
app-admin-panel

# agent
agent-data-quality-monitor
agent-ops-assistant
agent-invoice-processor
agent-lead-qualifier
agent-support-triage
agent-report-builder
agent-anomaly-detector
agent-onboarding-flow
agent-contract-reviewer
agent-monday-planner
```

Regras:
- Letras minúsculas e hífens apenas (sem underscores, sem maiúsculas)
- Descritivo o suficiente para entender o propósito sem abrir o README
- Evitar domínios genéricos como `tool`, `service`, `misc` ou `utils`

---

## Branches

### Branches permanentes

| Branch | Propósito |
|--------|-----------|
| `main` | Produção — sempre estável e deployável |
| `develop` | Integração — onde as features se juntam antes de ir para `main` |

### Branches de trabalho

```
feature/descricao-curta
fix/descricao-curta
chore/descricao-curta
refactor/descricao-curta
```

**Exemplos**

```
feature/fuzzy-matching-tokens
fix/duplicatas-monday-sync
chore/atualiza-dependencias
refactor/predicado-join-bigquery
```

Regras:
- Sempre partir de `develop`
- Deletar a branch após o merge
- Nunca commitar diretamente em `main` ou `develop`

### Proteção da `main` — repositórios de impacto médio/grande

A partir da versão `v1.0.0`, a branch `main` passa a exigir PR com ao menos 1 aprovação antes do merge.
Nenhum push direto é permitido, inclusive para administradores.

> Veja o procedimento completo em [docs/runbook-release-v1.md](./docs/runbook-release-v1.md).

---

## Commits — Conventional Commits

Formato: `<tipo>: <descrição no imperativo, em português>`

### Tipos

| Tipo | Quando usar |
|------|-------------|
| `feat` | Nova funcionalidade |
| `fix` | Correção de bug |
| `chore` | Tarefa de manutenção (deps, configs, CI) |
| `refactor` | Melhoria de código sem mudar comportamento |
| `docs` | Alterações em documentação |
| `test` | Adição ou correção de testes |
| `perf` | Melhoria de performance |
| `revert` | Reversão de commit anterior |

**Exemplos**

```
feat: adiciona fuzzy matching por token
fix: corrige duplicatas no monday sync
chore: atualiza dependências do projeto
refactor: otimiza predicado de join no BigQuery
docs: atualiza instruções de setup no README
test: adiciona cobertura para o módulo de parsing
```

Regras:
- Descrição em minúsculas, sem ponto final
- Máximo ~72 caracteres na primeira linha
- Se necessário, adicionar corpo após linha em branco explicando o **porquê**

---

## Versionamento — SemVer

Formato: `vMAJOR.MINOR.PATCH`

| Incremento | Quando |
|------------|--------|
| `MAJOR` | Mudança incompatível com versão anterior |
| `MINOR` | Nova funcionalidade compatível com versão anterior |
| `PATCH` | Correção de bug compatível com versão anterior |

**Exemplos**

```
v1.0.0   → release inicial
v1.1.0   → nova feature adicionada
v1.1.1   → bug corrigido
v2.0.0   → breaking change
```

Tags são criadas via GitHub Releases com changelog descrevendo o que mudou.

---

## Pull Requests

- Título segue o mesmo formato de commit: `feat: descrição`
- Referenciar issue relacionada quando aplicável: `Closes #42`
- Pelo menos 1 aprovação antes do merge
- Squash merge para manter histórico limpo em `main`

---

## Estrutura mínima de repositório

```
README.md          # O que é, como instalar, como usar
.gitignore         # Arquivos a ignorar (usar templates do GitHub)
```

Para serviços e dados, adicionar também:
```
docs/              # Documentação técnica adicional
```

---

## Veja também

- [docs/onboarding.md](docs/onboarding.md) — Primeiros passos para novos membros
- [docs/security.md](docs/security.md) — Regras de segurança e credenciais
- [docs/runbook-release.md](docs/runbook-release.md) — Passo a passo de um release
- [docs/runbook-release-v1.md](docs/runbook-release-v1.md) — Checklist de proteção pós-v1.0
