# Segurança

Regras de segurança aplicadas a todos os repositórios da organização.

---

## Segredos e credenciais

- **Nunca** commitar credenciais, tokens, senhas ou chaves de API
- Usar `.env` localmente — o arquivo está no `.gitignore`
- Sempre fornecer um `.env.example` com as variáveis necessárias (sem valores reais)
- Credenciais de serviços externos ficam em Secret Manager (GCP) ou nos Secrets do repositório no GitHub

## Permissões de repositório

| Papel | Permissão |
|-------|-----------|
| Mantenedor | Admin |
| Desenvolvedor | Write |
| Externo / consultor | Read ou Write por repositório específico |

## Branch protection em `main`

Todos os repositórios devem ter as seguintes regras ativas em `main`:

- Require pull request before merging
- Require at least 1 approval
- Dismiss stale reviews when new commits are pushed
- Do not allow bypassing the above settings

## Reporte de vulnerabilidades

Encontrou uma vulnerabilidade? Não abra uma issue pública.
Entre em contato diretamente com o responsável da org em: <!-- email ou canal privado -->

---

## Veja também

- [../CONTRIBUTING.md](../CONTRIBUTING.md) — Convenções de branches e commits
- [onboarding.md](onboarding.md) — Primeiros passos para novos membros
- [runbook-release.md](runbook-release.md) — Passo a passo de um release (inclui o marco v1.0)
