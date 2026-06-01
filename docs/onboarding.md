# Onboarding

Passo a passo para um novo membro começar a contribuir.

---

## 1. Acessos necessários

Solicite ao responsável da org:

- [ ] Convite para a organização GitHub
- [ ] Acesso ao projeto no GCP (se aplicável)
- [ ] Acesso ao workspace do Monday.com
- [ ] Acesso ao Slack / canal de comunicação da equipe

---

## 2. Configuração do ambiente local

### Git

```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu@email.com"
```

### Python (projetos de dados)

```bash
python -m venv .venv
source .venv/bin/activate      # Linux/macOS
.venv\Scripts\activate         # Windows

pip install -r requirements.txt
```

### Variáveis de ambiente

Cada repositório tem um `.env.example` na raiz.
Copie e preencha com seus valores:

```bash
cp .env.example .env
```

---

## 3. Leia antes de abrir seu primeiro PR

- [Guia de Contribuição](../CONTRIBUTING.md)
- [Segurança](./security.md)

---

## 4. Fluxo de trabalho padrão

```bash
git checkout main
git pull
git checkout -b feature/minha-tarefa

# faça suas alterações

git add .
git commit -m "feat: descrição do que foi feito"
git push origin feature/minha-tarefa

# abra o PR no GitHub apontando para main
```

---

## Veja também

- [../CONTRIBUTING.md](../CONTRIBUTING.md) — Convenções de branches e commits
- [security.md](security.md) — Regras de segurança e credenciais
- [runbook-release.md](runbook-release.md) — Passo a passo de um release
- [runbook-release-v1.md](runbook-release-v1.md) — Checklist de proteção pós-v1.0
