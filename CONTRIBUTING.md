# Guia de Contribuição

Convenções e boas práticas adotadas em todos os repositórios desta organização.

---

## Nomenclatura de repositórios

Formato base: `{domínio}-{descritor}`

Para repositórios de ferramentas, agentes e workers com escopo de área bem definido, use o formato estendido: `{domínio}-{área}-{descritor}`

| Domínio | Uso |
|---------|-----|
| `data`  | Pipelines, ETL, ingestão, modelos dbt |
| `api`   | Serviços HTTP com contrato externo; tools de IA expostas como endpoint |
| `svc`   | Serviços backend de aplicação — lógica de negócio interna, sem contrato HTTP externo |
| `worker`| Jobs background, sync, automações; skills de IA executadas como processo |
| `infra` | IaC, Terraform, configuração de ambiente e CI/CD |
| `lib`   | Código reutilizável; tools de IA importadas como biblioteca por outros repos |
| `app`   | Interfaces, frontends, dashboards |
| `agent` | Sistemas autônomos com objetivo, memória e orquestração de tools/skills |
| `docs`  | Repositórios de documentação — handbooks, wikis, bases de conhecimento |

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
>
> **Dúvida entre `api` e `svc`?**
> Se expõe contrato HTTP consumido por sistemas externos → `api`.
> Se é serviço backend de uso interno da aplicação → `svc`.
>
> **Quando usar `docs`?**
> Só quando a documentação é a entrega principal do repositório.
> Documentação de um sistema específico vive no `docs/` do próprio repo.

> **Repositório de demanda pontual (roda uma vez, sob demanda de outra área)?**
> Frequência de execução não é critério de domínio — o domínio classifica o que o repo *é*, não com que frequência roda. Uma conciliação de dados executada uma única vez continua sendo `data`; uma automação disparada uma vez continua sendo `worker`. Se o resultado precisa de auditoria/rerun futuro, versione normalmente (commits, tags por execução) e documente contexto (data, solicitante, escopo) no README — não crie um domínio novo pra isso.

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

# svc (serviços backend de aplicação)
svc-billing
svc-notifications
svc-pricing-engine
svc-user-management

# worker (jobs, sync e skills de IA como processo)
worker-monday-sync
worker-invoice-generator
worker-email-notifications
worker-slack-alerts
worker-data-quality-checks
worker-fiscal-cnpj-status
worker-fiscal-nfe-emitter

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
agent-finance-receipt-validator
agent-finance-invoice-processor
agent-commercial-lead-qualifier
agent-support-triage
agent-legal-contract-reviewer
agent-monday-planner

# docs
docs-handbook-comercial
docs-onboarding-eng
docs-processos-internos
```

Regras:
- Letras minúsculas e hífens apenas (sem underscores, sem maiúsculas)
- Descritivo o suficiente para entender o propósito sem abrir o README
- Evitar domínios genéricos como `tool`, `misc` ou `utils`; para serviços backend use `svc` (nunca `service` por extenso)
- Para `agent`, `worker` e `api` com escopo de área claro, prefira o formato estendido `{domínio}-{área}-{descritor}` (ex.: `agent-finance-receipt-validator`, `worker-fiscal-cnpj-status`); use o formato curto apenas quando a área for óbvia ou irrelevante para distinguir o repo

---

## Branches

### Branches permanentes

| Branch | Propósito |
|--------|-----------|
| `main` | Produção — sempre estável e deployável |

### Branches de trabalho

```
feature/descricao-curta
fix/descricao-curta
chore/descricao-curta
refactor/descricao-curta
release/vX.Y.Z
```

**Exemplos**

```
feature/fuzzy-matching-tokens
fix/duplicatas-monday-sync
chore/atualiza-dependencias
refactor/predicado-join-bigquery
release/v1.2.0
```

Regras:
- Sempre partir de `main`
- Deletar a branch após o merge
- Nunca commitar diretamente em `main`
- `release/vX.Y.Z`: usada quando múltiplas features precisam ser integradas antes de ir para `main` — features são mergeadas na release branch, depois um único PR vai para `main`

### Proteção da `main` — repositórios de impacto médio/grande

A partir da versão `v1.0.0`, a branch `main` passa a exigir PR com ao menos 1 aprovação antes do merge.
Nenhum push direto é permitido, inclusive para administradores.

> Veja o procedimento completo em [docs/runbook-release.md](./docs/runbook-release.md#marco--primeira-release-v10).

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
- Squash merge para manter histórico limpo em `main`

**Quando PR com revisão é obrigatório:**
- Repositório em produção (pós-v1.0) de impacto médio/grande — ao menos 1 aprovação
- Aplicação crítica onde interrupção é inaceitável

**Quando PR não é necessário:**
- Projeto pré-v1.0 ou ainda em desenvolvimento
- Repositório solo sem revisor disponível — merge direto em `main` após confirmação

---

## Testes

Testes são obrigatórios apenas para artefatos que chegam a produção.
Durante o desenvolvimento não há exigência — a autonomia prevalece.

| Tipo | Testes |
|------|--------|
| Notebook / análise exploratória | Não |
| Script ad-hoc one-shot | Não |
| Módulo / função reutilizável em produção (`utils`, connectors, transformações) | Assertions simples ou doctest nos casos não-óbvios |
| Pipeline agendado em produção (BigQuery, GCP) | Sim — shape, tipos, nulls, intervalos esperados nas transformações críticas |
| App / API em produção | Sim — testes de integração nos endpoints e lógica de negócio |

**Regra:** se roda sem supervisão (agendado, CI/CD) ou é consumido por outros sistemas em produção → testa.

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
- [docs/runbook-release.md](docs/runbook-release.md) — Passo a passo de um release (inclui o marco v1.0)
- [docs/ai-observability.md](docs/ai-observability.md) — Observabilidade e conformidade de IA: contrato de log e gatilhos sensíveis
- [docs/runbook-ai-logging.md](docs/runbook-ai-logging.md) — Passo a passo para instrumentar logs de IA
