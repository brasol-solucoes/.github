# Runbook — Lançamento de Release na `main`

Procedimento padrão para cortar um release: do commit à tag publicada.
Fluxo: `feature/* → develop → main → tag`.

> Este runbook é o **passo a passo operacional**. As regras (tipos de commit, SemVer, nomenclatura) vivem no [CONTRIBUTING.md](../CONTRIBUTING.md) — aqui apenas as aplicamos.

---

## Visão geral do fluxo

```
feature/minha-tarefa ──PR──► develop ──PR──► main ──tag──► v1.2.0 (GitHub Release)
```

---

## 1. Durante o desenvolvimento — commits

Trabalhe sempre em uma branch de feature partindo de `develop`:

```bash
git checkout develop
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

## 2. Abrir o PR e integrar em `develop`

O `git push` apenas envia a branch — abrir o PR é um passo separado. Duas formas:

**Pelo site:** após o push, o GitHub mostra o botão **"Compare & pull request"** e o output do push imprime um link direto (`.../pull/new/feature/descricao-curta`). Clique, confirme `develop` como base e crie.

**Pela CLI ([GitHub CLI](https://cli.github.com/)):**

```bash
gh pr create --base develop --head feature/descricao-curta \
  --title "feat: descrição" --body "O que mudou e por quê"
```

Depois de aberto:

- Garanta que o CI (testes/lint) está verde
- Merge via **squash** mantendo a mensagem no padrão de commit
- Delete a branch de feature após o merge

Repita os passos 1–2 até que `develop` reúna tudo que entra no release.

---

## 3. Abrir o PR de release (`develop → main`)

Quando `develop` estiver pronto para produção:

1. Abra um PR de `develop` para `main`
2. Título no padrão: `release: vX.Y.Z`
3. Na descrição, liste o changelog (o que entrou desde o último release)
4. Referencie issues fechadas: `Closes #42, #57`

> **Checklist antes de pedir revisão**
> - [ ] Todos os testes/checks verdes
> - [ ] `develop` atualizado com a `main` (sem conflitos)
> - [ ] Changelog escrito na descrição do PR

---

## 4. Pedir revisão

- Marque ao menos **1 revisor** (`@eng` ou `@devops`, conforme o domínio)
- Para repositórios de impacto médio/grande pós-v1.0, a aprovação é **obrigatória** — ver [runbook-release-v1.md](./runbook-release-v1.md)
- Resolva todos os comentários antes do merge (conversation resolution)

---

## 5. Merge na `main`

Após a aprovação:

- Merge via **squash**
- Mensagem final no padrão Conventional Commits
- Confirme que o deploy de produção (se houver CI/CD) disparou com sucesso

---

## 6. Definir a versão — SemVer

Decida o incremento com base no que mudou desde a última tag:

| Incremento | Quando |
|------------|--------|
| `MAJOR` (`v2.0.0`) | Mudança incompatível / breaking change |
| `MINOR` (`v1.1.0`) | Nova funcionalidade compatível |
| `PATCH` (`v1.0.1`) | Apenas correção de bug |

> Na dúvida entre MINOR e PATCH: se um usuário ganha algo novo → MINOR; se só conserta algo quebrado → PATCH.

---

## 7. Criar a tag e o GitHub Release

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

## 8. Pós-release

- [ ] Comunicar o release no canal `#general` ou no canal do time no Slack
- [ ] Confirmar que produção está estável
- [ ] Garantir que `develop` está sincronizado com a `main` (caso o merge tenha alterado a main)

```bash
git checkout develop
git merge main
git push origin develop
```

---

## Veja também

- [../CONTRIBUTING.md](../CONTRIBUTING.md) — Convenções de branches, commits e SemVer
- [runbook-release-v1.md](runbook-release-v1.md) — Ativação da proteção da `main` na v1.0
- [security.md](security.md) — Regras de segurança e credenciais
