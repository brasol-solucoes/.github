# Arquitetura Geral

Visão de alto nível dos sistemas e como eles se conectam.

---

## Diagrama de contexto

```
                        ┌─────────────┐
                        │  Fonte de   │
                        │   dados     │
                        └──────┬──────┘
                               │
                        ┌──────▼──────┐
                        │  Pipeline   │
                        │  de dados   │
                        └──────┬──────┘
                               │
               ┌───────────────┼───────────────┐
               │               │               │
        ┌──────▼─────┐  ┌──────▼─────┐  ┌──────▼─────┐
        │  BigQuery  │  │  Monday.com│  │   API REST │
        └────────────┘  └────────────┘  └────────────┘
```

> Substitua pelo diagrama real da sua stack.

---

## Componentes

### Pipeline de dados
- **Repositório:** `data-pipeline-aneel`
- **Responsabilidade:** ingestão, transformação e carga de dados
- **Stack:** Python, BigQuery, Cloud Functions / Airflow

### API Comercial
- **Repositório:** `service-api-commercial`
- **Responsabilidade:** exposição de dados e operações para o front-end
- **Stack:** descreva aqui

### Sync Monday
- **Repositório:** `tool-monday-sync`
- **Responsabilidade:** manter dados do Monday.com em sincronia com o sistema interno
- **Stack:** descreva aqui

---

## Decisões de arquitetura

Registre aqui decisões relevantes que não ficam óbvias no código.

| Data | Decisão | Motivo |
|------|---------|--------|
| 2026-05 | BigQuery como data warehouse central | escala, custo e integração com GCP |
