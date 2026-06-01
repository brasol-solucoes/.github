# Relatório de Configuração — GitHub Brasol

Documento de referência consolidando tudo que foi configurado na organização [brasol-solucoes](https://github.com/brasol-solucoes) no GitHub: configurações da org, políticas, convenções e localização da documentação.

> Última atualização: 2026-06-01

---

## 1. Configurações da organização

| Configuração | Status | Descrição |
|--------------|--------|-----------|
| Foto de perfil | ✅ | Logo oficial da Brasol |
| Descrição | ✅ | Descrição da organização no perfil público |
| Billing Email | ✅ | E-mail de cobrança definido |
| Autenticação em dois fatores (2FA) | ✅ | Obrigatória para todos os membros da org |
| Política de criação de repositórios | ✅ | Restrição de quem pode criar repositórios |
| Landing page | ✅ | Perfil público em `github.com/brasol-solucoes` |

### Times (Teams)

| Time | Responsabilidade |
|------|------------------|
| `@eng` | Engenharia — aplicações, APIs, AI agents, pipelines de dados |
| `@devops` | Infraestrutura, cloud, CI/CD e segurança |

---

## 2. Repositório central `.github`

O repositório [`.github`](https://github.com/brasol-solucoes/.github) é o nome especial reconhecido pelo GitHub para centralizar configurações e documentação válidas para toda a organização.

```
.github/
├── README.md                      # Apresentação do repositório
├── CONTRIBUTING.md                # Convenções de desenvolvimento
├── profile/
│   ├── README.md                  # Landing page pública da org
│   └── banner.jpg                 # Banner do perfil
└── docs/
    ├── onboarding.md              # Guia para novos membros
    ├── security.md                # Políticas de segurança
    ├── runbook-release-v1.md      # Checklist de proteção da main
    └── setup-report.md            # Este documento
```

---

## 3. Convenções de desenvolvimento

> Documento completo: [CONTRIBUTING.md](../CONTRIBUTING.md)

### 3.1 Nomenclatura de repositórios

Formato: `{domínio}-{descritor}`

| Domínio | Uso |
|---------|-----|
| `data`  | Pipelines, ETL, ingestão, modelos dbt |
| `api`   | Serviços HTTP; tools de IA expostas como endpoint |
| `worker`| Jobs background, sync; skills de IA como processo |
| `infra` | IaC, Terraform, configuração de ambiente e CI/CD |
| `lib`   | Código reutilizável; tools de IA importadas como biblioteca |
| `app`   | Interfaces, frontends, dashboards |
| `agent` | Sistemas autônomos com objetivo, memória e orquestração |

Regras: minúsculas e hífens apenas; descritivo; evitar domínios genéricos (`tool`, `service`, `misc`).

### 3.2 Branches

| Branch | Propósito |
|--------|-----------|
| `main` | Produção — sempre estável e deployável |
| `develop` | Integração de features antes da `main` |

Branches de trabalho: `feature/`, `fix/`, `chore/`, `refactor/`.
Regras: partir de `develop`; nunca commitar direto em `main` ou `develop`; deletar branch após merge.

### 3.3 Commits — Conventional Commits

Formato: `<tipo>: <descrição no imperativo>`
Tipos: `feat`, `fix`, `chore`, `refactor`, `docs`, `test`, `perf`, `revert`.

### 3.4 Versionamento — SemVer

Formato: `vMAJOR.MINOR.PATCH`. Tags criadas via GitHub Releases com changelog.

### 3.5 Pull Requests

- Título no formato de commit
- Referência a issues (`Closes #42`)
- Mínimo 1 aprovação
- Squash merge

---

## 4. Políticas de segurança

> Documento completo: [security.md](./security.md)

### 4.1 Segredos e credenciais

- Nunca commitar credenciais, tokens ou chaves
- Usar `.env` local (ignorado pelo git) + `.env.example` versionado
- Credenciais em Secret Manager (GCP) ou GitHub Secrets

### 4.2 Permissões de repositório

| Papel | Permissão |
|-------|-----------|
| Mantenedor | Admin |
| Desenvolvedor | Write |
| Externo / consultor | Read ou Write por repositório |

### 4.3 Branch protection em `main`

- Require pull request before merging
- Require at least 1 approval
- Dismiss stale reviews when new commits are pushed
- Do not allow bypassing the above settings

### 4.4 Reporte de vulnerabilidades

Não abrir issue pública — contato direto com o responsável da org.

---

## 5. Proteção da `main` e maturidade

> Documento completo: [runbook-release-v1.md](./runbook-release-v1.md)

Repositórios de impacto médio/grande passam a exigir revisão em PR a partir da `v1.0.0`.

| Fase | Período | Proteção na `main` |
|------|---------|-------------------|
| 0 — Exploração | antes do primeiro commit estável | sem proteção |
| 1 — Desenvolvimento | pré-v1.0 | PR obrigatório, sem review |
| 2 — Produção | pós-v1.0 | PR + 1 aprovação + checks |

A transição entre fases é manual (não há automação no GitHub baseada em tag).

---

## 6. Onboarding de novos membros

> Documento completo: [onboarding.md](./onboarding.md)

Cobre: solicitação de acessos (GitHub, GCP, Monday, Slack), configuração do ambiente local (Git, Python, variáveis de ambiente) e o fluxo de trabalho padrão de branches e PRs.

---

## 7. Onde encontrar cada coisa

| Preciso de... | Onde |
|---------------|------|
| Como nomear um repo / branch / commit | [CONTRIBUTING.md](../CONTRIBUTING.md) |
| Começar como novo membro | [docs/onboarding.md](./onboarding.md) |
| Regras de segurança e credenciais | [docs/security.md](./security.md) |
| Ativar proteção da `main` na v1.0 | [docs/runbook-release-v1.md](./runbook-release-v1.md) |
| Perfil público da org | [profile/README.md](../profile/README.md) |
