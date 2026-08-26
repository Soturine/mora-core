# DevOps, DevSecOps e Release Engineering

> **Status:** padrão operacional de desenvolvimento. CI/CD executável será criado quando existir aplicação correspondente; não criar pipeline ornamental.

## Objetivo

Manter o ciclo completo reproduzível:

```text
plan
→ code
→ build
→ test
→ package
→ deploy
→ verify
→ operate
→ learn
```

Segurança, QA e observabilidade entram no fluxo, não como revisão tardia.

---

# Git e fluxo de mudança

## Pequenos lotes

Preferir commits lógicos e revisáveis:

```text
feature/regra
+ testes correspondentes
+ docs/migration quando necessário
```

Evitar commit gigante com refactor + dependency update + feature + formatação sem relação.

## Push remoto real

Mudança só entra no fluxo remoto quando push confirmado. CI pode rodar assíncrona enquanto trabalho independente continua.

## Main

Direct-main pode ser aceitável em equipe pequena se gates e branch protection evoluírem proporcionalmente. Para mudanças de alto risco, PR/second review é preferível.

Nunca force-push/rewrite de branch integrada sem motivo extraordinário e coordenação.

## SHA exato

Release/tag/deploy precisa apontar ao mesmo SHA que passou gates.

Antes de release:

```text
HEAD == origin/main
CI green on exact SHA
artifact = artifact testado
```

---

# Ambientes

```text
local
staging
production
```

Possíveis ephemeral previews quando justificadas.

## Isolamento

- bancos separados;
- credentials separados;
- buckets separados ou namespaces/controles fortes;
- marketplace sandbox/test account quando disponível;
- staging não usa production secret.

## Config drift

Infrastructure/config deve ser versionada quando possível. Evitar mudanças manuais invisíveis.

---

# Build

## Reprodutibilidade

- lockfile committed;
- runtime/version declarada;
- builds determinísticos na medida possível;
- dependencies pinned/resolvidas;
- no baixar script aleatório em CI sem controle.

## Artifact

Preferir artifact/container/package imutável.

Fluxo:

```text
commit
→ build artifact
→ test artifact
→ promote same artifact
```

Evitar rebuild diferente para produção.

---

# CI

Gates dependem da fase/stack.

Baseline futuro:

1. install frozen;
2. format check;
3. lint;
4. typecheck;
5. unit/component;
6. integration conforme superfície;
7. architecture/contract checks;
8. build;
9. security scans;
10. artifact.

Full suite/E2E pesado não precisa bloquear todo commit se custo alto; usar milestone/release/blast radius.

## Concurrency

Cancelar runs superseded quando seguro para reduzir gasto e ruído.

## Permissions

GitHub Actions com `permissions` mínimas. Jobs de PR de fork não recebem secrets privilegiados.

---

# DevSecOps gates

## SAST

CodeQL ou ferramenta adequada ao stack.

## SCA

Dependency vulnerability scanning e dependency review.

## Secret scanning

Pre-commit/CI/GitHub capabilities conforme ambiente.

## IaC scanning

Quando Terraform/Pulumi/etc existir.

## Container scanning

Quando containers existirem.

## SBOM e provenance

Adicionar em release relevante quando produto/artifact distribuído justificar.

## Findings

- Critical/High revisados cedo;
- não marcar falso positivo sem justificativa;
- issue/owner quando deferred;
- medium/low por risco/contexto.

Segurança não vira burocracia de bloquear tudo sem triagem.

---

# Dependências

Antes de adicionar:

- necessidade;
- manutenção;
- licença;
- maturidade;
- tamanho/transitive deps;
- security history;
- alternativa nativa.

## Dependabot

Útil, mas **sem auto-merge cego**.

Patch/minor com testes podem ser rápidos; major/churn exige avaliação.

---

# Secrets

## Fonte

Secret manager/provider de CI, nunca Git.

## Regras

- least privilege;
- por ambiente;
- rotação;
- short-lived/OIDC quando provider suporta;
- logs redigidos;
- acesso auditado;
- no frontend/APK.

## Vazamento

Tratar como incidente: revogar primeiro, depois limpar história/causa conforme necessidade.

---

# Infrastructure as Code

Quando infraestrutura deixar de ser trivial, versionar:

- compute;
- DB config;
- buckets;
- queues;
- DNS;
- CDN/WAF;
- IAM;
- monitoring.

Não criar Kubernetes só para cumprir “IaC”.

## State

State de IaC protegido, remoto, backup/locking quando ferramenta exigir.

---

# Database migrations

## Regras

- versionadas;
- testadas em DB real;
- backward-compatible quando possível;
- timeout/lock impact considerado;
- backfill observável/idempotente;
- não rodar `drop` destrutivo sem plano.

## Expand / migrate / contract

Para mudança arriscada:

1. adicionar estrutura compatível;
2. deploy código que entende ambos;
3. migrar/backfill;
4. verificar;
5. remover estrutura antiga em release posterior.

## Backup não é rollback

Restore pode ser demorado e perder dados após backup. Migration precisa estratégia própria.

---

# Deploy

Estratégia inicial pode ser rolling/simple deploy com health check, dependendo de provider.

Quando risco justificar:

- blue/green;
- canary;
- feature flags.

Não adotar mecanismo complexo sem necessidade.

## Feature flags

Cada flag precisa:

- owner;
- propósito;
- default seguro;
- observability;
- plano de remoção/expiração.

Flag não substitui authorization/entitlement.

---

# Rollback

Antes do deploy responder:

- artifact anterior está disponível?
- schema é compatível?
- migration impede rollback?
- external side effects são reversíveis?
- feature flag pode desligar?

Em muitos casos, forward-fix é mais seguro que rollback de DB; documentar.

---

# Post-deploy verification

Não declarar sucesso só porque plataforma disse “deployed”.

Verificar:

- live/readiness;
- error rate;
- logs;
- DB/migrations;
- job backlog;
- integration health;
- CUJ smoke apropriada.

Exemplo:

```text
create/read fixture staging
→ auth denial
→ inventory operation
→ persistence
```

Produção usa smoke não destrutiva/segura.

---

# Release

Milestone/release exige:

- exact SHA;
- gates green;
- migrations revisadas;
- docs atuais;
- changelog/release notes;
- known issues;
- rollback/forward plan;
- security findings triaged;
- artifact provenance quando adotada.

## SemVer

Quando houver versão pública, usar SemVer ou convenção explícita. Breaking API/comportamento precisa comunicação/migration.

---

# Observabilidade no pipeline

Guardar:

- SHA;
- build ID;
- artifact digest;
- deployment ID;
- ambiente;
- horário;
- actor/automation.

Logs de app podem incluir release SHA para correlação.

---

# Backups e DR no release engineering

Mudança de alto risco pode exigir backup/checkpoint antes, mas backup precisa restore testado periodicamente.

Runbook de migration failure e rollback existe antes de produção crítica.

---

# FinOps de CI/cloud

Monitorar:

- minutes CI;
- artifact storage;
- preview envs;
- container registry;
- DB;
- egress;
- IA jobs.

Cancelar recursos órfãos e runs superseded.

---

# Supply chain avançada

Quando maturidade justificar:

- SBOM CycloneDX/SPDX;
- signed artifacts;
- SLSA/provenance;
- OIDC federation;
- policy checks.

Não fazer claims de nível/compliance sem evidência.

---

# Definition of Done técnica

Uma mudança só é `Done` quando aplicável:

- regra/acceptance criteria atendidos;
- testes focados;
- docs/migration;
- lint/typecheck;
- security concerns;
- push remoto;
- CI green ou status explicitamente aguardado;
- manual QA quando necessário;
- observability;
- sem segredo/placeholder.

## Status honesto

- `implementado`;
- `parcial`;
- `experimental`;
- `adiado`;
- `não validado`.

## Relacionados

- [QA](../qa/test-strategy.md)
- [Segurança](../security/security-architecture.md)
- [SRE](../operations/sre-aiops.md)
- [Engineering Constitution](../engineering/constitution.md)
