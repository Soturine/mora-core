# Riscos, Assunções, Dependências e Questões em Aberto

> **Status:** registro vivo. Uma questão aberta não é um gap acidental quando está aqui com impacto e condição de resolução.

## Como usar

Para cada risco relevante registrar:

```text
Risco/questão
Categoria
Probabilidade
Impacto
Sinais
Mitigação
Contingência
Owner
Condição de resolução
Status
```

Não inventar respostas para deixar documentação “bonita”.

---

# 1. Escopo

## R-001 — Construir tudo cedo demais

**Risco:** ERP + PDV + fiscal + mobile + site + IA + marketplaces + billing simultaneamente.

**Impacto:** atraso, baixa qualidade, sistema nominalmente amplo e operacionalmente frágil.

**Mitigação:** roadmap por fases, WIP baixo, Discovery e gates.

## R-002 — Feature parity com concorrentes

Benchmark virar backlog automático.

**Mitigação:** exigir problema/usuário/métrica antes de capacidade entrar no roadmap ativo.

---

# 2. Discovery/negócio

## Q-001 — Topologia legal

- quantos CNPJs?
- qual loja pertence a qual entidade?
- estoque pode cruzar entidades?

**Bloqueia:** schema final de LegalEntity, fiscal e transferências interempresa.

## Q-002 — Comissão

- percentual;
- bruto/líquido;
- desconto;
- devolução;
- troca;
- promo;
- fechamento/pagamento.

**Bloqueia:** CommissionPlan produtivo.

## Q-003 — Seller vs cashier real

Modelo suporta ambos; validar se venda compartilhada por item existe.

## Q-004 — Troca/devolução

Semântica comercial/fiscal precisa de exemplos reais.

## Q-005 — Estoques/depósitos

Loja == location? Há depósito? Transferência imediata?

## Q-006 — Hardware

**Status: parcialmente resolvido em 05/09/2026.** Existe agora um [baseline real de PDV e hardware](../discovery/mora-pos-hardware-baseline-2026-09-05.md) cobrindo os dois caixas de referência, PCs, leitores e impressoras observados.

Ainda falta confirmar:

- portas, drivers e modo de operação dos leitores;
- impressora/gaveta e pinagem/configuração real;
- impressora de etiquetas, se separada;
- modelo/adquirente dos terminais de pagamento;
- TEF/Pix realmente integrado;
- rede, link reserva e nobreak;
- equipamentos reserva;
- política de ciclo de vida/upgrade das estações.

## Q-024 — Ownership NOOVA/Lips/Bling

A loja feminina/familiar foi observada usando NOOVA e a masculina usando Lips Control. Ainda precisa ser definido, por capability, quem é a autoridade atual de catálogo, estoque, venda, caixa, pagamento e fiscal e como Bling participa do fluxo.

**Bloqueia:** plano de integração/migração, shadow POS e cutover.

## Q-025 — Arquitetura do cliente POS e periféricos

Decidir por ADR/POC entre:

- PWA/web + Local Device Bridge;
- wrapper desktop leve;
- cliente desktop dedicado;
- arquitetura híbrida.

A escolha precisa ser provada no hardware real das duas lojas, não por preferência tecnológica.

---

# 3. Fiscal/pagamentos

## R-003 — Complexidade fiscal brasileira

Mudanças legais, contingência, certificados e rejeições podem paralisar venda.

**Mitigação:** provider/ERP existente inicialmente; contador; homologação; adapter/runbook.

## Q-007 — Provider fiscal

Bling atual, Nuvem Fiscal, TecnoSpeed ou outro? Decisão por ADR/POC.

## Q-008 — TEF/adquirentes/Pix

O levantamento visual confirmou que existem terminais físicos e que o NOOVA possui entradas de administração/gerenciamento relacionadas a TEF e formas de pagamento, mas **o fluxo real e a autoridade ainda não estão confirmados**.

Validar:

- adquirente/modelo;
- TEF vs POS desacoplado;
- Pix integrado/manual;
- parcelamento/crediário;
- conciliação;
- timeout/cancelamento.

## R-004 — Duplicar cobrança/documento em retry

**Mitigação:** idempotência + status query/reconciliation.

---

# 4. Multi-tenancy

## R-005 — Vazamento cross-tenant

Um dos riscos mais críticos de SaaS.

**Mitigação:** server-side scope, object authz, tests, cache/job context, RLS defense-in-depth possível.

## R-006 — Suporte com acesso excessivo

**Mitigação:** support tooling mínimo, reason/audit/time-bound; nenhuma impersonation por default.

## Q-009 — Estratégia enterprise dedicada

DB/schema dedicado pode existir futuramente; não implementar antes de cliente/requisito.

---

# 5. Estoque

## R-007 — Race/overselling

Venda e reserva concorrentes podem consumir última unidade.

**Mitigação:** transactions/constraints/locks, reservations, safety stock, tests.

## Q-010 — Negative stock

A tela de produtos do legado feminino mostrou exemplos de saldo negativo e resíduos/fracionamentos em itens `UN`. Isso confirma que a policy do Core **não pode ser deduzida do legado**.

Precisamos decidir:

- estoque negativo será proibido, permitido por exceção ou apenas importado como anomalia histórica?
- como reconciliar saldos de abertura?
- como tratar resíduos fracionários em unidade inteira?

Ver [Migração dos legados Mora](../data/mora-legacy-data-migration.md).

## R-008 — Drift balance/ledger

**Mitigação:** reconciliation e balance reconstruível.

## R-009 — Offline duplica movimento

**Mitigação:** operationId/outbox/idempotency.

## R-035 — Importar anomalias do legado como verdade do Core

**Risco:** milhares de registros históricos podem conter saldo negativo/fracionário, ausência de categoria, custo desconhecido/zero, duplicidades ou grade inconsistente.

**Mitigação:** snapshot bruto, staging, anomaly report, mapping, dry-run, reconciliação e aprovação antes de `OPENING_IMPORT`.

---

# 6. Mobile/offline

## R-010 — Prometer offline total cedo

Fiscal, pagamento e conflicts tornam isso complexo.

**Mitigação:** começar inventário/drafts; PDV offline só com spec própria.

## Q-011 — React Native + Expo

Direção preferida, mas validar câmera, barcode, SQLite, printing, secure storage e distribution em spike/ADR.

## R-011 — Dados locais em aparelho perdido

**Mitigação:** secure storage, minimização, session revocation, lifecycle/cache protection.

---

# 7. IA

## R-012 — Alterar aparência real da peça

**Impacto:** anúncio enganoso/devolução/política de canal.

**Mitigação:** Product Truth, original, provenance, eval, human review.

## R-013 — Hallucination em descrição

**Mitigação:** structured facts/unknown, schema, review.

## R-014 — Custo imprevisível

**Mitigação:** AI Gateway, quota/metering/budget/FinOps.

## R-015 — Prompt injection/tool abuse

**Mitigação:** untrusted input, tool allowlist, sem DB/shell irrestritos.

## Q-012 — Providers/modelos

Não congelados; avaliar qualidade, privacidade, custo, latência e termos.

---

# 8. Marketplaces

## R-016 — APIs/políticas mudam

**Mitigação:** adapters/versioned compliance, official docs, contract tests.

## R-017 — Shopee access/contrato não confirmado

Não congelar endpoints até onboarding/Open Platform atual.

## R-018 — Rate limits/backlog

**Mitigação:** queue, backpressure, concurrency, reconciliation.

## R-019 — Publicar IA onde canal proíbe/restringe

**Mitigação:** channel policy snapshot + revalidation no publish.

## Q-013 — Ordem de canais

Qual primeiro: TikTok Shop, Mercado Livre ou Shopee? Depende do plano real das lojas.

---

# 9. Bling/migração

## R-020 — Duas fontes de verdade

Core e Bling/legados escrevendo estoque simultaneamente sem ownership.

**Mitigação:** fases explícitas de transição; one source per capability.

## R-021 — Importar dívida de dados

SKUs/categorias duplicadas/variações ruins entram no Core.

**Mitigação:** audit, staging, mapping, dry-run, reconciliation.

O levantamento de 05/09/2026 tornou esse risco concreto: o legado observado contém milhares de produtos e indícios de dívida de classificação/saldo.

## Q-014 — Quais módulos Bling são realmente usados

Discovery obrigatório, agora junto com o mapeamento de NOOVA/Lips.

## R-036 — Migração big-bang dos dois PDVs

**Risco:** substituir simultaneamente NOOVA, Lips, hardware, fiscal e pagamentos sem período comparativo.

**Mitigação:** extract/read-only → Core projection → shadow → piloto controlado → cutover por capability com fallback.

---

# 10. Storefront

## R-022 — Site builder virar plataforma genérica cedo

**Mitigação:** componentes/presets seguros, sem arbitrary JS/CSS inicialmente.

## R-023 — Dados demo virarem claims reais

**Mitigação:** `verified` vs `demonstration`, no fake Offer/best seller.

## Q-015 — Checkout próprio

WhatsApp pode ser suficiente inicialmente; validar demanda antes de payments/logistics completos.

---

# 11. Segurança/supply chain

## R-024 — Secret em Git/frontend/mobile

**Mitigação:** secret scanning, manager, architecture boundary.

## R-025 — Dependency compromise

**Mitigação:** lockfile, SCA, Dependabot review, CodeQL, minimal deps, SBOM/provenance quando necessário.

## R-026 — Upload malicioso

**Mitigação:** MIME/content, budgets, isolated storage, scan when justified.

## R-037 — Estações legadas e software local aumentam superfície de ataque

O levantamento mostrou Windows 10 em uma estação, Windows 11 em hardware antigo na outra e softwares locais/remote-support. Isso exige inventory e hardening antes de transformar o caixa em endpoint confiável do Mora Core.

**Mitigação:** verificar lifecycle real do SO, patches, contas administrativas, remote support, software desnecessário, drivers, firewall, atualização assinada do cliente/bridge e plano de renovação de hardware.

## R-038 — Local Device Bridge inseguro

Um bridge localhost mal desenhado pode permitir site malicioso imprimir, abrir gaveta ou executar comandos.

**Mitigação:** bind local, autenticação/origin allowlist, contrato estreito, command allowlist, schema validation, rate limit, least privilege, update assinado e nenhum shell/SQL/raw command.

---

# 12. Dados/LGPD

## R-027 — Coletar PII sem finalidade

**Mitigação:** privacy inventory/minimization.

## R-028 — Deleção incompleta

DB excluído, CDN/derivados/exports permanecem.

**Mitigação:** lifecycle por representação.

## Q-016 — Retenções legais

Definir com apoio jurídico/contábil.

---

# 13. Infra/DevOps

## Q-017 — Backend/framework

Fastify, NestJS ou alternativa ainda não congelados.

## Q-018 — ORM/query layer

Avaliar SQL ergonomics, migrations, transactions e type safety.

## Q-019 — Cloud/provider

Escolher por custo, managed PostgreSQL, storage, jobs, observability, região e operação.

## Q-020 — Queue/jobs

Postgres-backed inicialmente pode bastar; provar antes de Kafka/Rabbit/SQS etc.

## R-029 — Migration destrutiva

**Mitigação:** expand/migrate/contract, tests, backups, rollout.

## R-030 — CI verde mas feature quebrada

**Mitigação:** anti-fake-smoke, integration/CUJ/manual QA.

---

# 14. SRE/operação

## Q-021 — SLO/RPO/RTO

Precisam de objetivo real, não números decorativos.

## R-031 — Alert fatigue

**Mitigação:** actionable alerts + runbooks.

## R-032 — Backup nunca restaurado

**Mitigação:** restore exercises.

## R-039 — Periférico local vira single point of failure do caixa

**Risco:** scanner, impressora, gaveta, driver ou bridge indisponível pode bloquear atendimento se não houver fallback.

**Mitigação:** entrada manual, reprint, health check, modo degradado, dispositivo reserva quando custo justificar, runbook e teste de desconectar/reconectar.

---

# 15. SaaS comercial

## Q-022 — Planos/preços

Não definidos.

## Q-023 — Billing provider

Não escolhido.

## R-033 — Pricing de IA sem unit economics

**Mitigação:** metering e cost attribution antes de planos “ilimitados”.

## R-034 — Onboarding exige suporte manual demais

**Mitigação:** medir time-to-value e automatizar provisioning/import gradualmente.

---

# Assunções atuais

São hipóteses, não fatos permanentes:

1. PostgreSQL é boa base transacional inicial.
2. Monólito modular é melhor que microserviços no estágio inicial.
3. React Native + Expo provavelmente atende mobile.
4. Bling deve ser integrado antes de substituído onde ele realmente for autoridade/capability relevante.
5. Sites atuais podem migrar incrementalmente para Catalog API.
6. Moda é domínio inicial forte, mas plataforma pode atender outros varejos.
7. IA será assistiva/human-reviewed.
8. SaaS usará DB compartilhado multi-tenant inicialmente com isolamento rigoroso.
9. O futuro POS deve abstrair periféricos por capability e ser validado no hardware real antes de definir seu runtime.

Cada uma pode virar ADR e ser revisada com evidência.

# Registro de decisão

Quando questão é resolvida:

- criar/atualizar ADR;
- remover de “aberta” ou marcar resolvida;
- atualizar docs dependentes;
- adicionar testes/gates se necessário.

## Relacionados

- [Discovery](../discovery/operational-discovery.md)
- [Baseline real de PDV/hardware](../discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [POS e periféricos](../architecture/pos-device-integration.md)
- [Migração dos legados Mora](../data/mora-legacy-data-migration.md)
- [ADRs](../adr/README.md)
- [Roadmap](roadmap.md)
- [Cobertura](documentation-coverage.md)
