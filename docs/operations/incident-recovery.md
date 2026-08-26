# Incidentes, Backup e Disaster Recovery

> **Status:** política operacional planejada. Severidades, RPO/RTO e contatos finais devem ser definidos antes do go-live.

## Objetivo

Recuperar o Mora Core com segurança quando falhas inevitáveis ocorrerem, preservando dados, evidência e comunicação.

## Princípios

- detectar cedo;
- conter antes de “consertar no escuro”;
- preservar evidência;
- recuperar serviço e integridade;
- validar após recovery;
- comunicar com clareza;
- postmortem sem culpabilização;
- transformar incidente em melhoria.

---

# Ciclo

```text
Detect
→ Triage
→ Declare
→ Contain
→ Diagnose
→ Mitigate/Recover
→ Validate
→ Communicate
→ Close
→ Postmortem
→ Follow-up
```

## Incident commander

Em incidente relevante, uma pessoa coordena decisões/comunicação para evitar ações conflitantes. Papéis podem ser leves em equipe pequena.

---

# Severidade

Política final depende do negócio. Exemplo:

## SEV-1

- cross-tenant data exposure;
- perda/corrupção ampla;
- pagamentos duplicados em escala;
- sistema de venda indisponível sem contingência;
- credencial crítica comprometida.

## SEV-2

- integração importante parada;
- backlog de pedidos;
- estoque divergente em vários canais;
- site indisponível sem afetar PDV;
- fiscal degradado com contingência.

## SEV-3

- erro localizado com workaround;
- relatório incorreto não financeiro crítico;
- feature não essencial degradada.

Não usar nomenclatura sem SLA/expectativas correspondentes.

---

# Classes prioritárias

## Segurança

- tenant leak;
- account takeover;
- secret leak;
- malicious upload;
- supply chain compromise.

## Dados

- DB corruption;
- stock divergence;
- failed migration;
- accidental delete;
- media loss.

## Commerce

- sale duplication;
- payment ambiguity;
- marketplace overselling;
- order stuck;
- fiscal outage.

## Infra

- DB unavailable;
- queue backlog;
- storage outage;
- DNS/certificate;
- deploy failure.

---

# Comunicação

Durante incidente registrar:

- impacto;
- início;
- sistemas/tenants afetados;
- workaround;
- próxima atualização.

Evitar especular causa antes de evidência.

Para SaaS público, definir status page/canais quando maturidade justificar.

## Privacidade

Incidente de dados segue avaliação jurídica/LGPD de notificação aplicável. Não declarar obrigação específica sem revisão do caso.

---

# Backups

## PostgreSQL

Planejar:

- backup automatizado;
- encrypted;
- retenção;
- PITR quando necessidade justificar;
- cópia/controle resistente a exclusão acidental/ransomware;
- acesso mínimo.

## Object storage

Avaliar:

- versioning;
- lifecycle;
- replication conforme risco;
- proteção de deletion.

## Config/IaC

Git é parte da recuperação, mas secrets não estão no Git. Documentar como restaurar config/infra/credentials.

---

# Restore testado

Exercício periódico:

```text
novo ambiente isolado
→ restore DB no ponto escolhido
→ restore/reconectar storage
→ deploy artifact compatível
→ migrations/check
→ run smoke
→ validar tenants/invariantes
→ medir tempo
→ registrar gaps
```

## O que validar

- usuário consegue login fixture;
- tenant isolation;
- catálogo;
- balance x movements;
- venda histórica;
- mídia;
- jobs/inbox state;
- storefront conforme escopo.

---

# RPO e RTO

## RPO

Quanto histórico podemos perder?

## RTO

Quanto tempo a jornada pode ficar fora?

Valores devem vir de conversa com negócio e exercícios. Podem diferir por capability:

- PDV;
- site;
- analytics;
- IA.

Não usar um número universal se impacto difere.

---

# Disaster Recovery

Documentar dependências necessárias para reconstruir:

```text
source/release artifacts
IaC
configuration
secrets recovery procedure
PostgreSQL backup/PITR
object storage
DNS/domain
observability
integration credentials/reconnect
```

## Região/provider outage

Multi-region não é requisito inicial. DR pode usar restore em mesma/outra região conforme RPO/RTO e provider. Testar antes de prometer.

---

# Migration failure

Runbook:

1. parar rollout;
2. avaliar locks/dados;
3. não aplicar rollback SQL automático sem entender;
4. usar backward-compatible deploy/forward fix quando seguro;
5. restore somente com impacto conhecido;
6. verificar integridade.

Migration destrutiva exige plano antes do deploy.

---

# Stock divergence

1. congelar operações afetadas se necessário;
2. identificar locations/variants;
3. comparar ledger/balance/source externo;
4. preservar eventos;
5. corrigir via movimento explícito/rebuild autorizado;
6. reconciliation;
7. root cause.

Não editar balance para “bater”.

---

# Secret leak

1. revogar/rotate imediatamente;
2. identificar exposição/escopo;
3. invalidar sessões/tokens relacionados;
4. revisar logs/audit;
5. trocar dependências/config;
6. comunicar conforme impacto;
7. eliminar causa.

Remover secret do Git não o torna seguro se já foi exposto.

---

# Marketplace/provider outage

- preservar pedidos/eventos;
- pausar retries agressivos;
- circuit/concurrency control;
- informar operação sobre impacto;
- reconciliar quando provider voltar;
- evitar overselling com safety policy.

---

# Fiscal outage

Usar contingência definida com provider/contador. Não inventar emissão offline em incidente.

---

# Postmortem

Documento contém:

- resumo;
- impacto;
- timeline;
- detection;
- resposta;
- causas/contribuintes;
- o que funcionou;
- o que falhou;
- ações priorizadas;
- owners/prazos;
- prevenção/detecção.

Foco em sistema e processo, não culpa individual.

## Action items

Precisam ser específicos e rastreáveis. “Ter mais cuidado” não é ação suficiente.

---

# Chaos/failure testing

Só quando arquitetura/ambiente permitir, em staging ou produção controlada:

- matar worker;
- provider 500;
- latency;
- token expiry;
- DB failover;
- network partition simulada.

Não introduzir chaos em operação imatura sem guardrails.

---

# Evidência de recovery

Guardar:

- data;
- versão;
- backup usado;
- RPO obtido;
- RTO obtido;
- checks;
- falhas;
- melhorias.

## Relacionados

- [Runbooks](runbook-catalog.md)
- [Data lifecycle](../data/data-lifecycle.md)
- [SRE](sre-aiops.md)
- [Segurança](../security/security-architecture.md)
