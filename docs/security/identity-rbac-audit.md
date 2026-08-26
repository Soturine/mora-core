# Identidade, RBAC, Escopos e Auditoria — Segurança

> **Status:** baseline de segurança. O modelo de negócio detalhado está em [Identidade, Funcionários e Permissões](../domain/identity-employees-permissions.md).

## Objetivo

Definir controles de acesso sem reduzir autorização a `ADMIN`/`USER`.

## Conceitos

```text
User                 identidade digital
Employee             pessoa/colaborador
OrganizationMembership vínculo com tenant
RoleAssignment        conjunto de capacidades
StoreScope            lojas permitidas
Permission/Capability ação permitida
AuditEvent             evidência de ação
```

## AuthN != AuthZ

Autenticar prova identidade/sessão; não prova que pode acessar um recurso.

Toda ação privada:

```text
session válida
→ membership ativa
→ tenant correto
→ capability
→ object authorization
→ store scope
→ state policy
```

## Roles

Papéis iniciais:

- `OWNER`;
- `ADMIN`;
- `MANAGER`;
- `CASHIER`;
- `SELLER`;
- `STOCK_OPERATOR`;
- `ACCOUNTANT`;
- `VIEWER`.

Role é bundle inicial de capabilities e pode ser configurável no futuro. Não usar role string como autorização universal dentro de cada controller.

## Capabilities

Exemplos:

```text
sale.create
sale.cancel
sale.discount.apply
sale.discount.approve
cash.open
cash.close
cash.withdraw
inventory.receive
inventory.adjust
inventory.transfer
commission.view.self
commission.view.organization
commission.configure
product.publish
integration.manage
user.manage
```

## Escopos

Capability pode valer:

- organização inteira;
- uma ou várias lojas;
- recurso próprio (`self`);
- location específica.

Exemplo: `commission.view.self` não concede leitura da comissão de outro employee alterando ID na URL.

## Object-level authorization

Teste obrigatório contra BOLA/IDOR:

```text
GET /employees/B/commission
```

com usuário A deve falhar mesmo se ambos estiverem na mesma organização, quando policy só permite `self`.

## Tenant isolation

Nunca confiar apenas na cláusula de URL/body.

Repositórios/use cases recebem contexto autorizado. Queries usam tenant scope e resource checks.

RLS futuro pode complementar.

## Ações privilegiadas

Podem exigir step-up/approval:

- desconto acima de limite;
- cancelamento;
- sangria;
- ajuste de estoque;
- alterar comissão;
- conectar marketplace;
- export de PII;
- gerir owner/admin;
- offboarding.

## Aprovação

Não pedir “senha do gerente” e guardar/reusar.

Fluxo seguro:

```text
request
→ policy exige approval
→ manager autentica/step-up
→ Approval record
→ ação executada
```

Registrar approver e reason sem secret.

## Sessões

Requisitos futuros:

- expiração;
- revogação;
- device/session list;
- logout global;
- refresh rotation quando aplicável;
- MFA;
- risco de dispositivo perdido;
- invalidar após desligamento/role change conforme policy.

## Convites

- token aleatório;
- hash/segurança no armazenamento;
- expiração;
- single-use;
- tenant/role bound;
- revoke;
- rate limit.

## Lifecycle de funcionário

Desligar:

```text
Employee inactive
Membership/role removed
User access disabled/revoked conforme caso
```

Histórico de venda/comissão permanece.

## Auditoria

Registrar ações com relevância de negócio/segurança:

```text
actorUserId
employeeId?
organizationId
storeId?
action
resourceType/resourceId
before/after sanitizado
reason
approvalId?
requestId
occurredAt
```

Audit não registra password/token/CVV.

## Integridade do audit

- append-only por aplicação;
- acesso restrito;
- mudanças administrativas auditadas;
- retenção definida;
- export controlado;
- considerar storage/controle adicional quando risco justificar.

Não afirmar “imutável criptograficamente” sem implementar.

## Suporte SaaS

Admin da plataforma não deve bypassar tenant por default.

Ferramenta futura:

- selecionar tenant explicitamente;
- reason/ticket;
- permission privilegiada;
- tempo limitado;
- audit;
- mascarar PII quando possível.

## Testes essenciais

- role matrix;
- store scope;
- self vs others;
- cross-tenant IDs;
- inactive membership;
- revoked session;
- invite replay;
- approval required;
- privilege escalation;
- audit generated;
- support boundary.

## Relacionados

- [Domínio de identidade](../domain/identity-employees-permissions.md)
- [Segurança](security-architecture.md)
- [Multi-tenancy](../saas/multitenancy.md)
