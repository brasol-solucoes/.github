# Runbook — Lançamento de Release na `main`

Procedimento padrão para cortar um release: do commit à tag publicada.
Dois caminhos dependendo do escopo do release:

- **Feature única:** `feature/* → main`
- **Multi-feature:** `feature/* → release/vX.Y.Z → main → tag`

> Este runbook é o **passo a passo operacional**. As regras (tipos de commit, SemVer, nomenclatura) vivem no [CONTRIBUTING.md](../CONTRIBUTING.md) — aqui apenas as aplicamos.

---

## Visão geral dos fluxos

```
# feature única
feature/minha-tarefa ──PR──► main ──tag──► v1.2.0 (GitHub Release)

# multi-feature
feature/feat-a ──PR──┐
feature/feat-b ──PR──► release/v1.2.0 ──PR──► main ──tag──► v1.2.0
feature/feat-c ──PR──┘
```

---

## 1. Durante o desenvolvimento — commits

Trabalhe sempre em uma branch de feature partindo de `main`:

```bash
git checkout main
git pull
git checkout -b feature/descricao-curta
```

Faça commits no padrão [Conventional Commits](../CONTRIBUTING.md#commits--conventional-commits):

```bash
git commit -m "feat: adiciona fuzzy matching por token"
git commit -m "fix: corrige duplicatas no monday sync"
```

Suba a branch:

```bash
git push origin feature/descricao-curta
```

---

## 2. Abrir PR para `main`

O `git push` apenas envia a branch — abrir o PR é um passo separado. Duas formas:

**Pelo site:** após o push, o GitHub mostra o botão **"Compare & pull request"** e o output do push imprime um link direto (`.../pull/new/feature/descricao-curta`). Clique, confirme `main` como base e crie.

**Pela CLI ([GitHub CLI](https://cli.github.com/)):**

```bash
gh pr create --base main --head feature/descricao-curta \
  --title "feat: descrição" --body "O que mudou e por quê"
```

Depois de aberto:

- Garanta que o CI (testes/lint) está verde
- Merge via **squash** mantendo a mensagem no padrão de commit
- Delete a branch de feature após o merge

> Para releases com múltiplas features, veja o **Fluxo multi-feature** abaixo antes de abrir o PR.

---

## Fluxo multi-feature — `release/vX.Y.Z`

Use quando múltiplas features precisam ser integradas antes de ir para `main`.

**1. Criar a branch de release a partir de `main`:**

```bash
git checkout main
git pull
git checkout -b release/v1.2.0
git push origin release/v1.2.0
```

**2. Integrar as features na release branch:**

Cada feature abre PR com `--base release/v1.2.0`:

```bash
gh pr create --base release/v1.2.0 --head feature/minha-feature \
  --title "feat: descrição"
```

**3. Abrir PR de release para `main`:**

```bash
gh pr create --base main --head release/v1.2.0 \
  --title "release: vX.Y.Z" \
  --body "Changelog: o que entrou desde o último release. Closes #42, #57"
```

Checklist antes de pedir revisão:
- [ ] Todos os testes/checks verdes
- [ ] `release/vX.Y.Z` atualizada com `main` (sem conflitos)
- [ ] Changelog escrito na descrição do PR

Após merge: deletar a branch `release/vX.Y.Z`.

---

## 3. Pedir revisão

- Marque ao menos **1 revisor** (`@eng` ou `@devops`, conforme o domínio)
- Para repositórios de impacto médio/grande pós-v1.0, a aprovação é **obrigatória** — ver [Marco — primeira release (v1.0)](#marco--primeira-release-v10)
- Resolva todos os comentários antes do merge (conversation resolution)

---

## 4. Merge na `main`

Após a aprovação:

- Merge via **squash**
- Mensagem final no padrão Conventional Commits
- Confirme que o deploy de produção (se houver CI/CD) disparou com sucesso

---

## 5. Definir a versão — SemVer

Decida o incremento com base no que mudou desde a última tag:

| Incremento | Quando |
|------------|--------|
| `MAJOR` (`v2.0.0`) | Mudança incompatível / breaking change |
| `MINOR` (`v1.1.0`) | Nova funcionalidade compatível |
| `PATCH` (`v1.0.1`) | Apenas correção de bug |

> Na dúvida entre MINOR e PATCH: se um usuário ganha algo novo → MINOR; se só conserta algo quebrado → PATCH.

---

## 6. Criar a tag e o GitHub Release

Com a `main` já mergeada e atualizada localmente:

```bash
git checkout main
git pull
git tag -a v1.2.0 -m "Release v1.2.0"
git push origin v1.2.0
```

No GitHub:

1. Vá em **Releases → Draft a new release**
2. Selecione a tag `v1.2.0`
3. Título: `v1.2.0`
4. Cole o changelog (pode usar **Generate release notes**)
5. **Publish release**

---

## 7. Pós-release

- [ ] Comunicar o release no canal `#general` ou no canal do time no Slack
- [ ] Confirmar que produção está estável

---

## Marco — primeira release (v1.0)

A **v1.0.0** marca a virada de "projeto em desenvolvimento" para "produto em produção". A partir dela, repositórios de impacto médio/grande deixam de aceitar push direto: tudo passa a entrar via PR com aprovação obrigatória.

> Esta seção é executada **uma única vez por repositório**, no momento em que ele atinge a v1.0. Nas releases seguintes, o fluxo dos passos 1–7 acima já assume essa proteção ativa.

### Fases de maturidade do repositório

| Fase | Período | Proteção na `main` |
|------|---------|-------------------|
| 0 — Exploração | antes do primeiro commit estável | sem proteção |
| 1 — Desenvolvimento | pré-v1.0 | sem proteção formal |
| 2 — Produção | pós-v1.0 | PR + 1 aprovação + checks |

A virada entre fases é **manual** — o GitHub não ativa proteção automaticamente ao criar a tag. O checklist abaixo cobre a transição da Fase 1 para a Fase 2.

> **Responsável:** mantenedor com permissão de Admin no repositório.

### Checklist de ativação (executar ao publicar `v1.0.0`)

**1. Ativar branch protection na `main`**

1. Abra o repositório → **Settings → Branches**
2. Clique em **Add branch protection rule** (ou edite a existente)
3. Em **Branch name pattern**, coloque `main`
4. Marque:

| Opção | Marcar |
|-------|--------|
| Require a pull request before merging | ✅ |
| Require approvals → **1 aprovação mínima** | ✅ |
| Dismiss stale pull request approvals when new commits are pushed | ✅ |
| Require conversation resolution before merging | ✅ |
| Do not allow bypassing the above settings (bloqueia até admins) | ✅ |

5. **Save changes**

**2. Exigir status checks (se houver CI)**

- Marque **Require status checks to pass before merging**
- Adicione os checks relevantes (ex: `test`, `lint`, `build`)

**3. Confirmar que push direto está bloqueado**

```bash
git push origin main
# Esperado: remote: error: GH006: Protected branch update failed
```

**4. Comunicar a equipe** que, a partir da `v1.0.0`:

- Nenhum push direto em `main` é permitido
- Todo código entra via PR com ao menos **1 aprovação**
- O fluxo padrão passa a ser o descrito nos passos 1–7 deste runbook

---

## Veja também

- [../CONTRIBUTING.md](../CONTRIBUTING.md) — Convenções de branches, commits e SemVer
- [security.md](security.md) — Regras de segurança e credenciais
- [onboarding.md](onboarding.md) — Primeiros passos para novos membros
