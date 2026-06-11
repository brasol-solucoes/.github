# Observabilidade e Conformidade de IA

Política da organização para **logs, emissão e conformidade das execuções de IA** da Brasol — agentes, workflows, apps, skills e tools.

Toda IA que opera em nome da Brasol é auditável. Esta política define **o que precisa ser logado, com qual estrutura e sob quais regras** sempre que uma execução toca um ponto sensível. O serviço que observa e julga essas execuções é o **Pára-Raios** (`svc-para-raios-agent-auditor`); este documento é o contrato que todo app instrumenta, e o Pára-Raios consome.

> O **schema técnico versionado** (o evento canônico) é mantido no repo do Pára-Raios — ver [Contrato canônico de evento](#contrato-canônico-de-evento). Este documento descreve a **obrigação e as regras**, não a definição do schema (que não deve ser duplicada para não divergir).

---

## Quando esta política se aplica — os 4 gatilhos

Uma execução de IA está sob esta política quando faz **uma ou mais** das ações abaixo. São os "pontos sensíveis":

| # | Gatilho | Exemplo |
|---|---------|---------|
| **D** | Maneja **dado confidencial ou restrito** | lê uma tabela com PII, exporta registros de cliente |
| **C** | **Comunica em nome da Brasol** | envia e-mail, mensagem em Slack/Monday a terceiros |
| **S** | **Escreve em uma source of truth** | grava em BigQuery, atualiza item de board, escreve em Cloud SQL |
| **I** | **Influencia decisão estratégica/crítica** | recomenda crédito, prioriza lead, sugere preço |

Se a execução **não** toca nenhum gatilho, esta política não a obriga. Na dúvida, trate como sujeita — o custo de logar a mais é baixo; o de não observar uma ação sensível é legal e reputacional.

---

## Princípios invariantes

Estes princípios são o "porquê" das regras; valem mesmo quando a implementação evolui.

1. **Independência do auditor.** A observação não pode depender de o app se reportar. O backbone é *pull* (fontes que o app não controla — Cloud Logging, audit trails, audit logs nativos), mesmo quando há *push* de alta fidelidade por cima.
2. **Híbrido por discrepância.** Push e pull coexistem; divergência entre o que o app alega e o que de fato aconteceu é, ela própria, um achado.
3. **Contrato estável, fontes mutáveis.** O evento canônico é modelado nos 4 gatilhos, não no formato acidental de uma fonte. Fontes entram e saem; o contrato permanece.
4. **Minimização de dado (LGPD by design).** Quem audita dado confidencial jamais vira um novo repositório dele: log guarda **referência, hash e classificação — nunca o conteúdo**.
5. **Defensabilidade.** Todo alerta carrega evidência e a **versão exata da política/norma** que fundamentou o veredito.

---

## Modelo de emissão — pull hoje, push como alvo

| Modo | O que é | Estado | Obrigação do app |
|------|---------|--------|------------------|
| **Pull** | O Pára-Raios lê os logs nativos que o app já produz (audit trails em GCS, Cloud Logging, audit logs do BQ) | **Vigente** | Produzir **log estruturado** conforme a [estrutura mínima](#estrutura-mínima-de-log) para que o pull tenha sinal rico |
| **Push** | O app emite o evento canônico direto, via SDK/helper da Brasol | **Alvo (futuro)** | Adotar o SDK quando disponibilizado; até lá, garantir o pull |

> **Regra prática hoje:** você não precisa integrar SDK nenhum. Precisa **gravar um log estruturado** (não texto solto) descrevendo a ação sensível, com referências e classificação — e nunca o payload bruto. O Pára-Raios faz o resto via pull. Push é a direção, não um pré-requisito.

O contrato já nasce *push-shaped* justamente para que essa migração seja graciosa: a estrutura que você loga hoje para o pull é a mesma que o push preencherá amanhã.

---

## Contrato canônico de evento

O evento canônico descreve **uma ação de IA** de forma agnóstica de fonte, modelado nos 4 gatilhos. É o source of truth estável da observabilidade.

- **Definição versionada (autoridade do schema):** [`svc-para-raios-agent-auditor` → `docs/superpowers/specs/2026-06-11-agent-auditor-design.md` §4](https://github.com/brasol-solucoes/svc-para-raios-agent-auditor/blob/main/docs/superpowers/specs/2026-06-11-agent-auditor-design.md).
- **Forma resumida** (para orientação — a definição completa e o `schema_version` vigente estão no link acima):

```jsonc
{
  "event_id": "uuid",
  "schema_version": "1.0",
  "actor":  { "type": "agent|skill|tool|app", "name": "...", "version": "git-sha", "run_id": "..." },
  "source": { "system": "cloud_run|gcs|mcp|bigquery|cloud_sql", "collector": "...", "ingested_at": "RFC3339" },
  "occurred_at": "RFC3339",
  "action": { "type": "read|write|communicate|decide", "summary": "texto curto" },

  // os 4 gatilhos
  "data_touched":      [ { "resource": "...", "classification": "publico|interno|restrito|confidencial", "record_count": 0, "pii": false } ],
  "communication":     { "channel": null, "on_behalf_of_brasol": null, "recipients": [] },
  "sot_write":         { "system": null, "target": null, "change_ref": null },
  "decision_influence":{ "context": null, "recommendation_ref": null },

  "payload_ref": "gcs://.../sha256",   // REFERÊNCIA, nunca o conteúdo (LGPD)
  "integrity":  { "self_reported": true, "cross_checked": false, "discrepancies": [] }
}
```

**Versionamento:** todo evento carrega `schema_version`. Mudanças retrocompatíveis incrementam minor; quebras incrementam major. Mudanças no schema são feitas **no repo do Pára-Raios** e comunicadas; esta política não redefine campos.

---

## Estrutura mínima de log (o que o app deve produzir hoje)

Para que o pull funcione, a execução que toca um gatilho deve gravar um log **estruturado** (JSON), não texto livre, contendo no mínimo:

- **Identificação** — quem agiu (`actor.name`, `version`/git-sha, `run_id`) e quando (`occurred_at`).
- **Ação** — tipo (`read|write|communicate|decide`) e um resumo curto.
- **Sinal do(s) gatilho(s) tocado(s)** — o recurso afetado e sua **classificação**; para dado, a **contagem de registros** e flag de **PII**; para escrita em SoT, o **alvo** e a **referência da mudança**.
- **Referências, não conteúdo** — `payload_ref` (caminho + hash) em vez do payload. Nenhum dado confidencial/PII copiado para o log.

Ver o passo a passo de instrumentação em [runbook-ai-logging.md](runbook-ai-logging.md).

---

## Minimização de dado (LGPD)

Regra dura, sem exceção por padrão:

- **Nenhum payload bruto no log.** Guarde `payload_ref` (caminho + `sha256`), classificação e contagem.
- **PII é referenciada/contada, nunca copiada** para os stores de auditoria.
- Modo de captura de payload existe **apenas sob flag**, com retenção curta e acesso IAM restrito, e somente quando uma investigação formal exigir.
- Credenciais, tokens e segredos **nunca** aparecem em log — ver [security.md](security.md).

---

## Responsabilidades

| Papel | Responsabilidade |
|-------|------------------|
| **Dono do app/agente de IA** | Garantir que execuções que tocam gatilhos produzem log estruturado conforme esta política; nunca logar payload/PII; adotar o push SDK quando disponível |
| **Pára-Raios (auditor)** | Coletar via pull, enriquecer no contrato canônico, julgar conformidade com citação da norma, alertar e persistir trilha auditável |
| **Triagem humana / conformidade** | Revisar findings, decidir ação; o serviço alimenta a decisão com evidência, não a substitui |

---

## Veja também

- [security.md](security.md) — Segredos, credenciais e permissões (nunca logar segredo)
- [runbook-ai-logging.md](runbook-ai-logging.md) — Passo a passo para instrumentar um app/agente conforme esta política
- [../CONTRIBUTING.md](../CONTRIBUTING.md) — Nomenclatura de repos de IA (`agent`, `worker`, `lib`, `api`) e convenções
- [`svc-para-raios-agent-auditor`](https://github.com/brasol-solucoes/svc-para-raios-agent-auditor) — Serviço auditor; fonte versionada do contrato canônico (spec, vision, roadmap)
