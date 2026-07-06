<img src="banner.jpg" alt="Brasol" width="100%" />

Atuamos em 26 estados e no Distrito Federal, conectando empresas, investidores e comunidades a soluções de energia limpa, eficiente e economicamente viáveis.

<div align="center">

[![Site](https://img.shields.io/badge/brasol.co-2d2d2d?style=for-the-badge&logo=safari&logoColor=white)](https://brasol.co)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-2d2d2d?style=for-the-badge&logo=linkedin&logoColor=white)](https://br.linkedin.com/company/brasol-energy)
[![Email](https://img.shields.io/badge/contato@brasol.com-2d2d2d?style=for-the-badge&logo=gmail&logoColor=white)](mailto:contato@brasol.com)

</div>

---

## O que fazemos

- **Energia Solar** — Geração distribuída para empresas, indústrias e agronegócio
- **Armazenamento de Energia (BESS)** — Soluções em escala comercial, industrial, agrícola e utility
- **Subestações e Linhas de Transmissão** — Sale-leaseback, retrofitting, novas conexões e ampliações
- **Gestão de Ativos** — Operação, manutenção, monitoramento técnico e faturamento
- **Gestão de Investimentos** — Consultoria, gestão de fundos e soluções em mercado de capitais

---

## Brasol em números

- **R$ 2,2 bi+** investidos em projetos de energia no Brasil
- **200+** projetos desenvolvidos em todo o território nacional
- **500+** critérios de qualidade aplicados a cada projeto
- Padrões internacionais de gestão de risco e impacto socioambiental
- Relatório de Sustentabilidade publicado anualmente

---

## Contato

<div align="center">

| | |
|-|-|
| 🌐 **Site** | [brasol.co](https://brasol.co) |
| 📧 **E-mail** | contato@brasol.com |
| 📞 **Telefone** | +55 11 5505-6898 |
| 💼 **LinkedIn** | [brasol-energy](https://br.linkedin.com/company/brasol-energy) |
| 📍 **São Paulo** | Av. Eng. Luís Carlos Berrini, 1253 — Cidade Monções, SP |
| 📍 **Florianópolis** | R. Henrique Vera do Nascimento, 240 — Lagoa da Conceição, SC |

</div>

---

## Padrões de desenvolvimento

### Nomenclatura de repositórios — `{domínio}-{descritor}`

| Domínio | Uso |
|---------|-----|
| `data`   | Pipelines, ETL, ingestão, modelos dbt |
| `api`    | Serviços HTTP com contrato externo |
| `svc`    | Serviços backend de aplicação, sem contrato HTTP externo |
| `worker` | Jobs background, sync, automações |
| `infra`  | IaC, Terraform, CI/CD |
| `lib`    | Código reutilizável importado por outros repos |
| `app`    | Interfaces, frontends, dashboards |
| `agent`  | Sistemas autônomos que orquestram tools/skills |
| `docs`   | Repositórios de documentação — handbooks, wikis, bases de conhecimento |

Regras: letras minúsculas e hífens apenas. Descritivo o suficiente para entender o propósito sem abrir o README.

### Branches

| Branch | Propósito |
|--------|-----------|
| `main` | Produção — sempre estável e deployável |

Branches de trabalho: `feature/`, `fix/`, `chore/`, `refactor/`, `release/vX.Y.Z` — sempre partindo de `main`, deletadas após o merge. Nunca commitar direto em `main`.

### Commits — Conventional Commits

Formato: `<tipo>: <descrição em português, imperativo, sem ponto final>`

Tipos: `feat` · `fix` · `chore` · `refactor` · `docs` · `test` · `perf` · `revert`

```
feat: adiciona fuzzy matching por token
fix: corrige duplicatas no monday sync
chore: atualiza dependências do projeto
```

### Segurança

- Nunca commitar credenciais, tokens, senhas ou chaves de API
- Usar `.env` localmente — sempre fornecer `.env.example` com as variáveis necessárias (sem valores reais)
- Credenciais de serviços ficam no Secret Manager (GCP) ou nos Secrets do repositório no GitHub
- Vulnerabilidades: não abrir issue pública — contatar o responsável da org diretamente

---

## Ver também

- [Onboarding](../docs/onboarding.md) — Primeiros passos para novos membros da organização
- [Guia de Contribuição](../CONTRIBUTING.md) — Convenções de branches, commits e fluxo de trabalho
- [Segurança](../docs/security.md) — Regras de segurança e gestão de credenciais
- [Runbook de Release](../docs/runbook-release.md) — Passo a passo para lançamento de versões
