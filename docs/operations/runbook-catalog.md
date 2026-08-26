# Catálogo de Runbooks Operacionais

> **Status:** catálogo e templates. Runbooks passam a ser `executáveis` somente quando serviços, provedores, comandos e contatos reais existirem.

## Objetivo

Evitar descobrir durante um incidente que ninguém sabe:

- onde olhar;
- quem decide;
- qual ação é segura;
- como validar recuperação;
- quando escalar.

Um runbook não é um texto genérico de “reinicie o serviço”. Ele deve permitir execução segura por alguém treinado.

## Template obrigatório

Cada runbook futuro contém:

```text
Título
Severidade/impacto
Sinais/gatilhos
Pré-requisitos/permissões
Diagnóstico
Ações seguras passo a passo
Ações proibidas/cuidados
Rollback/fallback
Validação de recuperação
Comunicação
Escalonamento
Evidências/logs a preservar
Links/dashboards/comandos
Owner
Último teste
```

## Regras

- comandos destrutivos nunca aparecem sem contexto/checkpoint;
- secrets não entram no documento;
- links e nomes de dashboards são reais, não placeholders esquecidos;
- cada runbook crítico é exercitado em staging/simulação;
- revisão após incidente relevante;
- versão acompanha mudanças de arquitetura.

---

# Segurança

## RUN-SEC-001 — Secret/token exposto

Gatilhos:

- secret scanning;
- token em log/Git/screenshot;
- suspeita de credential theft.

Fluxo:

```text
confirmar exposição
→ revogar/rotacionar primeiro
→ identificar sistemas/tenants
→ invalidar dependências/sessões
→ preservar evidência
→ avaliar abuso
→ corrigir origem
→ comunicar conforme impacto
→ postmortem
```

Não considerar removido do Git como “seguro” sem rotação.

## RUN-SEC-002 — Suspeita de acesso cross-tenant

Prioridade máxima:

- conter caminho de acesso;
- preservar logs/audit;
- identificar tenants/recursos;
- não executar cleanup que destrua evidência;
- corrigir authorization/cache/job;
- revisar obrigação de notificação.

## RUN-SEC-003 — Conta privilegiada comprometida

- revogar sessões;
- reset credential/MFA;
- verificar alterações de roles/integrations/billing;
- audit timeline;
- rotacionar secrets acessíveis;
- avaliar tenants afetados.

## RUN-SEC-004 — Upload malicioso

- isolar asset;
- despublicar/invalidar CDN;
- analisar pipeline/consumidores;
- bloquear signature/hash quando apropriado;
- revisar validações.

---

# Banco e dados

## RUN-DATA-001 — PostgreSQL indisponível

Diagnóstico futuro:

- provider status;
- network/DNS;
- connection pool;
- storage;
- failover;
- credentials.

Evitar restart storm.

Validação pós-recuperação inclui transação read/write controlada e invariantes.

## RUN-DATA-002 — Restore PostgreSQL

Seguir procedimento testado de [Incidentes/DR](incident-recovery.md): ambiente isolado → restore → migrations → artifact compatível → smoke → invariantes.

## RUN-DATA-003 — Divergência de estoque

Nunca corrigir balance à mão sem movimento.

```text
variant/location
→ comparar movement ledger
→ balance
→ reservations
→ external mappings/events
→ identificar causa
→ adjustment/rebuild autorizado
→ reconcile
```

## RUN-DATA-004 — Migration falhou

- parar rollout;
- identificar estado parcial;
- preservar logs;
- verificar locks;
- aplicar forward fix/rollback compatível conforme plano;
- validar dados.

## RUN-DATA-005 — Mídia perdida/inacessível

- distinguir metadata vs bytes;
- provider/CDN/bucket;
- permissions;
- restore/version;
- regenerar derivados a partir de original quando possível.

---

# Integrações

## RUN-INT-001 — Bling indisponível

- verificar provider;
- pausar retries agressivos;
- manter inbox/outbox;
- operar dentro do modo/fonte de verdade da fase atual;
- reconciliar após retorno.

## RUN-INT-002 — Marketplace rate limited

- confirmar 429/rate headers;
- reduzir concurrency;
- respeitar Retry-After;
- priorizar pedidos/estoque crítico conforme policy;
- evitar retry storm.

## RUN-INT-003 — Token OAuth expirado/revogado

- tentar refresh seguro quando válido;
- marcar connection degraded;
- solicitar reconexão se necessário;
- não usar credential de outro tenant.

## RUN-INT-004 — Webhook backlog

- medir age/depth;
- provider/DB/worker bottleneck;
- aumentar workers dentro de rate limit;
- priorizar eventos críticos;
- reconciliation posterior.

## RUN-INT-005 — Schema drift

- isolar payload/version;
- bloquear aplicação incorreta;
- atualizar adapter/schema;
- replay eventos válidos após fix.

## RUN-INT-006 — Reconciliation encontrou drift

- quantificar;
- identificar direção da fonte de verdade;
- não sobrescrever em massa sem validar;
- corrigir mappings/eventos;
- reprocessar.

---

# Commerce / PDV

## RUN-COM-001 — Overselling

- identificar variante/pedidos/canais;
- bloquear nova venda/publicação se necessário;
- confirmar reservas e saldo físico;
- tratar pedidos afetados conforme política;
- corrigir stock projection;
- root cause (lag/race/mapping/manual).

## RUN-COM-002 — Venda duplicada

- não apagar histórico;
- identificar idempotency/payment/fiscal effects;
- fluxo de cancelamento/reversal correto;
- investigar origem do retry.

## RUN-COM-003 — Pagamento ambíguo/duplicado

- consultar provider antes de cobrar de novo;
- reconciliar IDs;
- refund somente por fluxo idempotente;
- preservar evidência.

## RUN-COM-004 — Caixa divergente

- impedir alteração silenciosa;
- revisar cash movements;
- pagamentos;
- sangria/suprimento;
- fechamento;
- approval/reason.

## RUN-COM-005 — Pedido preso

- estado OMS;
- pagamento;
- reserva;
- fiscal;
- fulfillment;
- channel status;
- replay apenas da etapa segura.

## RUN-COM-006 — Provedor fiscal indisponível

Usar contingência previamente homologada; nunca improvisar emissão fiscal.

---

# IA e mídia

## RUN-AI-001 — Custo anormal de IA

- confirmar tenant/capability/model;
- interromper jobs abusivos via kill switch/flag;
- preservar operações manuais;
- investigar loop/retry/abuso;
- ajustar quota.

## RUN-AI-002 — Modelo gera conteúdo incorreto em escala

- desabilitar capability/model version;
- impedir auto-publicação;
- identificar assets/textos afetados por provenance;
- restaurar/revisar versões;
- atualizar evals.

## RUN-AI-003 — Provider IA fora

Feature degrada para cadastro/edição manual; não bloquear estoque/venda.

---

# Plataforma/SaaS

## RUN-SAA-001 — Billing provider indisponível

Operação comercial dos tenants não deve parar automaticamente. Preservar subscription events e reconciliar depois.

## RUN-SAA-002 — Tenant onboarding preso

- identificar step;
- resource provisioning;
- compensation;
- retry idempotente;
- não criar owner/site/connection duplicados.

## RUN-SAA-003 — Domínio/TLS do storefront

- DNS ownership;
- certificate issuance;
- CDN/edge;
- fallback subdomain quando policy permitir;
- não instruir usuário a desativar segurança permanentemente.

---

# Infraestrutura

## RUN-OPS-001 — Rollback de deploy

- identificar SHA atual/anterior;
- migration compatibility;
- artifact digest;
- feature flags;
- rollback ou forward-fix;
- post-deploy smoke.

## RUN-OPS-002 — Queue crescendo

- oldest age;
- producer rate;
- worker errors;
- provider rate limit;
- DB saturation;
- scale dentro de budgets;
- backpressure.

## RUN-OPS-003 — Storage/DB perto do limite

- growth rate;
- cleanup legítimo;
- expand capacity;
- identificar runaway logs/media;
- não apagar dados autoritativos para liberar espaço sem policy.

## RUN-OPS-004 — Certificado/secret perto de expirar

- rotate em janela segura;
- testar nova versão;
- dual-key overlap quando provider permite;
- revogar antiga após validação.

---

# Exercícios

Antes de produção madura, testar ao menos:

- restore;
- token revogado;
- marketplace 429;
- worker down;
- failed migration em staging;
- secret leak simulado;
- AI provider outage;
- stock divergence fixture.

Registrar tempo, gaps e ações.

## Relacionados

- [Incidentes e DR](incident-recovery.md)
- [SRE/AIOps](sre-aiops.md)
- [Confiabilidade de integrações](../integrations/reliability-patterns.md)
- [Segurança](../security/security-architecture.md)
