# Runbook — Instrumentar logs de IA conforme a política

Passo a passo para tornar um app, agente, skill ou tool **conforme** com a [Política de Observabilidade e Conformidade de IA](ai-observability.md). Use quando criar ou ajustar uma IA que toca um ponto sensível.

> Este runbook é o **passo a passo operacional**. As regras e o porquê vivem em [ai-observability.md](ai-observability.md) — aqui apenas as aplicamos.

---

## Visão geral

```
1. Toca gatilho?  ──não──►  fora da política, nada a fazer
        │ sim
        ▼
2. Logar estruturado  ──►  3. Gravar onde o auditor lê  ──►  4. Minimizar dado  ──►  5. Validar
                                                                                          │
                                                              o Pára-Raios coleta via pull ◄┘
```

Hoje **não há SDK a integrar**: o trabalho é produzir um **log estruturado** que o Pára-Raios lê via pull. Push é a fase futura (passo 6).

---

## 1. Identificar se o app toca um gatilho

Sua execução faz alguma destas? (os 4 gatilhos)

- **D** — lê/exporta dado restrito ou confidencial (PII, dado de cliente)?
- **C** — comunica em nome da Brasol (e-mail, Slack, Monday a terceiros)?
- **S** — escreve em source of truth (BigQuery, Cloud SQL, board)?
- **I** — influencia decisão estratégica/crítica (recomenda, prioriza, precifica)?

Nenhum → fora do escopo. Um ou mais → siga. Na dúvida, trate como sujeito.

---

## 2. Emitir log estruturado

Para cada ação sensível, grave **um registro JSON** (não `print`/texto solto) com os campos da [estrutura mínima](ai-observability.md#estrutura-mínima-de-log). Exemplo — agente lê tabela com PII e escreve resultado em SoT:

```python
import json, hashlib, logging

logger = logging.getLogger("ai-audit")

def log_acao_sensivel(run_id, recurso, classificacao, n_registros, pii, payload_bytes, alvo_sot):
    payload_ref = "gcs://brasol-apps-prod-audit/payloads/" + hashlib.sha256(payload_bytes).hexdigest()
    evento = {
        "schema_version": "1.0",
        "actor":  {"type": "agent", "name": "credit-qualification-app",
                   "version": "git-sha-aqui", "run_id": run_id},
        "occurred_at": "2026-06-11T14:03:00Z",        # RFC3339
        "action": {"type": "read", "summary": "qualificação lê histórico do cliente"},
        "data_touched": [{"resource": recurso, "classification": classificacao,
                          "record_count": n_registros, "pii": pii}],
        "sot_write": {"system": "bigquery", "target": alvo_sot, "change_ref": None},
        "payload_ref": payload_ref,                    # REFERÊNCIA, nunca o conteúdo
    }
    logger.info(json.dumps(evento, ensure_ascii=False))  # uma linha JSON por ação
```

Regras:
- **Uma linha JSON por ação sensível** — facilita o parsing no pull.
- Preencha apenas os blocos de gatilho que se aplicam; os demais podem sair ausentes/`null`.
- `version` deve permitir rastrear o código exato (git-sha ou tag).

---

## 3. Gravar onde o auditor lê (pull)

O Pára-Raios coleta via duas fontes. Garanta pelo menos uma:

| Fonte | Como o app grava | Quando usar |
|-------|------------------|-------------|
| **GCS audit trail** | escreve o JSON estruturado num bucket de runs (ex.: `gcs://brasol-apps-prod-audit/<app>/<run_id>.json`) | sinal rico; preferível quando o app já produz trilha estruturada |
| **Cloud Logging** | `logger.info(json.dumps(...))` — o Cloud Run captura `stdout`/`stderr` automaticamente | rede universal; cobre apps sem trilha em GCS |

Região e projeto seguem o padrão da casa (ex.: `us-central1`; projeto do app, ex.: `brasol-apps-prod` para produção ou `brasol-data-dev` em desenvolvimento). Não crie buckets fora do perímetro GCP da Brasol — ver convenções de infra.

---

## 4. Minimizar o dado (LGPD)

Antes de logar, confira:

- [ ] **Nenhum payload bruto** no log — só `payload_ref` (caminho + `sha256`).
- [ ] **Nenhuma PII** copiada — apenas `record_count` e `pii: true/false`.
- [ ] **Nenhum segredo/token/credencial** no log (ver [security.md](security.md)).
- [ ] Classificação do recurso preenchida (`publico|interno|restrito|confidencial`).

---

## 5. Validar

- [ ] Cada ação sensível gera **um** registro JSON parseável.
- [ ] Os campos de identificação (`actor`, `run_id`, `occurred_at`) estão presentes.
- [ ] Os blocos dos gatilhos tocados estão preenchidos com recurso + classificação.
- [ ] Rodando uma execução de teste, o JSON aparece no GCS audit trail e/ou no Cloud Logging.
- [ ] Nenhum payload/PII/segredo vazou para o log (revisar uma amostra real).

Com isso, o Pára-Raios passa a coletar, enriquecer e julgar a conformidade da sua IA automaticamente — sem mais trabalho do seu lado.

---

## 6. Futuro — migração para push (SDK)

Quando a Brasol disponibilizar o **helper/SDK de push**, o app emitirá o evento canônico direto (fidelidade máxima, menor custo de enriquecimento). Como a estrutura logada hoje já é *push-shaped*, a migração é graciosa: trocar `logger.info(json.dumps(...))` pela chamada do SDK. O pull permanece como ground-truth para cross-check de integridade. Acompanhe o roadmap do [`svc-para-raios-agent-auditor`](https://github.com/brasol-solucoes/svc-para-raios-agent-auditor).

---

## Veja também

- [ai-observability.md](ai-observability.md) — A política: gatilhos, princípios, contrato canônico e regras
- [security.md](security.md) — Segredos, credenciais e permissões
- [runbook-release.md](runbook-release.md) — Passo a passo de um release na `main`
