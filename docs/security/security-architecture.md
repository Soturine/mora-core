# Arquitetura de Segurança e Threat Model

> **Status:** baseline de segurança. Threat model deve ser atualizado a cada milestone e antes do SaaS público.

## Objetivo

Proteger dados, dinheiro, estoque, credenciais, tenants e continuidade operacional sem depender de “usuário não vai tentar isso”.

## Ativos críticos

- contas/sessões;
- tenant data;
- catálogo/custo/preço;
- estoque;
- vendas;
- pagamentos;
- caixa;
- comissões;
- clientes/funcionários;
- documentos fiscais;
- OAuth tokens;
- AI/API keys;
- backups;
- audit logs;
- mídia privada;
- CI/CD credentials.

## Trust boundaries

```text
Internet
→ Edge/API
→ Application
→ PostgreSQL/Storage/Jobs
→ External Providers
```

Clientes Web/Mobile/POS são não confiáveis para authorization/invariantes.

Cada integração externa é outra boundary.

## Ameaças prioritárias

### Multi-tenant

- BOLA/IDOR;
- query sem tenant scope;
- cache leak;
- job executando no tenant errado;
- export cross-tenant;
- support/impersonation abuse.

### Identidade

- credential stuffing;
- brute force;
- session theft;
- reset takeover;
- privilege escalation;
- convite reutilizado.

### Aplicação

- injection;
- XSS;
- CSRF conforme auth model;
- SSRF;
- mass assignment;
- path traversal;
- unsafe deserialization;
- open redirect.

### Domínio

- overselling por race;
- duplicar venda/pagamento;
- alterar comissão;
- fraude de desconto;
- ajuste de estoque sem rastro;
- caixa/fechamento manipulado;
- replay de webhook.

### Arquivos

- malware;
- decompression bomb;
- polyglot;
- MIME spoof;
- EXIF sensível;
- upload público indevido.

### Integrações

- token vazado;
- webhook spoof;
- OAuth CSRF/state mismatch;
- provider compromise;
- schema drift;
- rate-limit abuse.

### IA

- prompt injection;
- exfiltração via tool;
- conteúdo falso;
- cross-tenant context;
- custo abusivo;
- modelo/provider comprometido.

### Supply chain/infra

- dependency compromise;
- CI token leak;
- malicious action;
- IaC misconfig;
- public DB/storage;
- ransomware;
- backup deletion.

---

# Identidade

Baseline:

- hash moderno de senha se auth própria;
- MFA para owner/admin/ações privilegiadas quando aplicável;
- sessões expiradas e revogáveis;
- device/session management;
- rate limiting;
- recovery seguro;
- step-up para ações de risco.

Auth provider concreto precisa de ADR.

## Password reset

Não revelar existência de conta desnecessariamente. Tokens expiram e são single-use. Reset pode invalidar sessões conforme policy.

---

# Authorization

Separar:

```text
AuthN: quem é?
Membership: pertence a qual Organization?
Role/Capability: pode executar a ação?
Object AuthZ: pode agir neste recurso?
Store Scope: esta loja está no escopo?
State Policy: recurso permite esta transição?
```

Policies centralizadas e fail-closed.

UI esconder botão é UX, não segurança.

---

# Tenant isolation

- scope server-side;
- `organizationId` em recursos relevantes;
- repository/query guard;
- jobs carregam context;
- integration mapping inclui tenant;
- cache key inclui tenant onde privado;
- export/support passam authorization;
- testes adversariais.

RLS pode ser defesa em profundidade, não substitui aplicação.

---

# Operações sensíveis

Exemplos:

- desconto alto;
- cancelamento;
- sangria;
- ajuste;
- comissão;
- gestão de usuários;
- export PII;
- integração;
- billing;
- delete/offboarding.

Podem exigir:

- permission;
- step-up;
- approval;
- reason;
- audit.

---

# Secrets

Nunca em:

- Git;
- frontend;
- mobile APK;
- image/container layer;
- screenshot;
- logs;
- analytics event.

Usar secret manager/environment injection seguro, criptografia e rotação.

Separar secrets por ambiente/tenant/provider quando possível.

## Leak response

Runbook: revoke → rotate → audit access → assess impact → communicate quando necessário.

---

# Dados em trânsito/repouso

- TLS;
- encryption at rest do provider;
- criptografia adicional para tokens/credentials;
- signed URLs curtas;
- DB privado;
- backups criptografados;
- least privilege network.

Não afirmar “criptografia end-to-end” sem arquitetura real.

---

# Uploads

- allowlist;
- validar MIME por conteúdo;
- bytes/pixels/count budgets;
- server-generated keys;
- storage separado;
- no execute;
- sanitize metadata;
- malware scan quando risco justificar;
- download externo com SSRF protection;
- CDN público somente para asset publicado.

---

# Web

Quando UI existir:

- CSP adequada;
- output encoding;
- sem HTML arbitrário;
- CSRF control conforme sessão;
- cookie Secure/HttpOnly/SameSite quando aplicável;
- frame-ancestors;
- security headers;
- dependency hygiene.

---

# API

- schema validation;
- mass-assignment allowlist;
- request/response size limits;
- pagination;
- rate limits;
- BOLA tests;
- timeouts;
- error redaction;
- idempotency.

---

# OAuth de marketplaces

- `state` ligado à sessão/tenant;
- PKCE quando provider/flow aplicável;
- callback allowlist;
- scopes mínimos;
- refresh token protegido;
- revogação;
- no token browser storage se puder evitar;
- audit da conexão.

---

# Supply chain

Gates proporcionais:

- lockfile;
- dependency review;
- SCA;
- Dependabot sem auto-merge;
- CodeQL/SAST;
- secret scanning;
- IaC scanning;
- container scanning;
- SBOM/provenance em releases relevantes;
- GitHub Actions com permissões mínimas e pin adequado.

Dependência nova precisa de justificativa e manutenção conhecida.

---

# CI/CD

- branch/repo permissions;
- protected environments quando existir deploy;
- OIDC preferível a secrets cloud long-lived quando suportado;
- artifacts imutáveis;
- deploy do SHA validado;
- logs sem credentials;
- approval para produção conforme maturidade/risco.

---

# Banco

- private network;
- least privilege roles;
- migrations separadas de runtime quando adequado;
- constraints;
- no superuser na app;
- backups;
- PITR quando necessário;
- audit de ações administrativas.

---

# IA

- AI Gateway;
- tool allowlist;
- tenant context;
- PII minimization;
- prompt/output não confiável;
- quotas;
- human review;
- evals;
- provider key só backend.

---

# Logs e audit

Logs técnicos:

- sanitizados;
- retenção definida;
- correlation ID.

Audit:

- separado;
- acesso restrito;
- protegido de alteração casual;
- não guarda secrets.

---

# Privacidade/LGPD

Ver [Privacidade e LGPD](privacy-lgpd.md).

Segurança não é sinônimo de conformidade. Antes do SaaS público, mapear finalidade, bases, direitos, subprocessadores e retenção.

---

# Security testing

## Automatizado

- SAST;
- SCA;
- secret scanning;
- unit de policies;
- integration authz;
- API negative;
- dependency/IaC/container scans conforme superfície.

## Adversarial

- tenant A → B;
- ID enumeration;
- role escalation;
- upload malicioso;
- webhook replay;
- SSRF;
- race de estoque;
- idempotency reuse;
- prompt injection;
- export PII;
- cache leak.

## Independente

Milestones importantes devem receber auditoria independente/second reviewer. Pentest externo antes de SaaS com risco/dados relevantes quando justificável.

---

# Security gates por fase

### Foundation

Auth/authz, tenant isolation, DB, secrets, audit, CI scanning.

### Mobile

secure storage, lost device, offline cache, uploads.

### POS

payment/fiscal, cash permissions, device trust.

### Marketplaces

OAuth/webhooks/tokens/SSRF.

### SaaS público

LGPD, rate/abuse, support tools, billing, tenant adversarial, incident response.

---

# Incident classes

- cross-tenant exposure;
- privileged account takeover;
- secret leak;
- payment duplication;
- stock corruption;
- ransomware/data loss;
- malicious upload;
- supply chain.

Possuem runbook e postmortem.

## Relacionados

- [Identidade](../domain/identity-employees-permissions.md)
- [Privacidade](privacy-lgpd.md)
- [DevSecOps](../devops/devsecops.md)
- [Incidentes](../operations/incident-recovery.md)
