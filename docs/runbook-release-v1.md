# Runbook — Ativação de proteção da `main` no lançamento v1.0

Execute este checklist no momento em que o repositório atingir a versão 1.0.
Aplicável a repositórios de impacto médio ou grande.

---

## Quando executar

- Ao criar e publicar a tag `v1.0.0` no repositório
- Responsável: mantenedor com permissão de Admin no repo

---

## Checklist

### 1. Ativar branch protection na `main`

1. Abra o repositório no GitHub
2. Vá em **Settings → Branches**
3. Clique em **Add branch protection rule** (ou edite a regra existente)
4. Em **Branch name pattern**, coloque `main`
5. Marque as opções abaixo:

| Opção | Marcar |
|-------|--------|
| Require a pull request before merging | ✅ |
| Require approvals → **1 aprovação mínima** | ✅ |
| Dismiss stale pull request approvals when new commits are pushed | ✅ |
| Require conversation resolution before merging | ✅ |
| Do not allow bypassing the above settings (bloqueia até admins) | ✅ |

6. Clique em **Save changes**

---

### 2. Verificar status checks (se houver CI)

Se o repositório tiver GitHub Actions configurado:

1. Na mesma tela, marque **Require status checks to pass before merging**
2. Adicione os checks relevantes (ex: `test`, `lint`, `build`)

---

### 3. Confirmar que ninguém tem push direto na `main`

```bash
# Tente fazer push direto — deve ser rejeitado
git push origin main
# Esperado: remote: error: GH006: Protected branch update failed
```

---

### 4. Comunicar a equipe

Avise no canal da equipe que a partir da `v1.0.0`:

- Nenhum push direto em `main` é permitido
- Todo código entra via PR com ao menos **1 aprovação**
- O fluxo padrão é: `feature/* → develop → main`

---

## Fases de maturidade do repositório

| Fase | Período | Proteção na `main` |
|------|---------|-------------------|
| 0 — Exploração | antes do primeiro commit estável | sem proteção |
| 1 — Desenvolvimento | pré-v1.0 | PR obrigatório, sem review |
| 2 — Produção | pós-v1.0 | PR + 1 aprovação + checks |

> A virada entre fases é manual. Este runbook cobre a transição da Fase 1 para a Fase 2.

---

## Veja também

- [../CONTRIBUTING.md](../CONTRIBUTING.md) — Convenções de branches e commits
- [onboarding.md](onboarding.md) — Primeiros passos para novos membros
- [security.md](security.md) — Regras de segurança e credenciais
