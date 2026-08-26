# Identidade, RBAC, Escopos e Auditoria

## User != Employee

`User` representa identidade de acesso. `Employee` representa pessoa/colaborador no negócio. Um funcionário desligado pode ficar `active=false` sem apagar vendas/comissões históricas.

## Membership

Liga User a Organization e carrega roles/escopos. Uma pessoa pode pertencer a mais de uma organização.

## Roles iniciais

`OWNER`, `ADMIN`, `MANAGER`, `CASHIER`, `SELLER`, `STOCK_OPERATOR`, `ACCOUNTANT`, `VIEWER`. São ponto de partida, não autorização hardcoded universal.

## Escopo

Role pode valer na organização inteira ou lojas específicas. Policies verificam action + resource + tenant + store scope.

## Operações com step-up

Desconto acima de limite, cancelamento, sangria, ajuste de estoque e fechamento extraordinário podem exigir aprovação de gerente/owner.

## AuditLog

Registrar: actor, organization, store/context, action, resource type/id, before/after quando apropriado, reason, request/correlation ID, timestamp e metadata segura.

Audit não armazena secrets. Logs de auditoria precisam de proteção contra alteração indevida e retenção definida.

## Sessões

Suportar revogação, expiração, device/session list e MFA para perfis privilegiados quando produção SaaS exigir.
