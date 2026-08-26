# Identidade, Funcionários, Papéis e Autorizações

> **Status:** arquitetura planejada. Este documento define princípios e contratos de domínio; não implica implementação concluída.

## Objetivo

Modelar pessoas, contas e permissões de forma adequada para uma operação real de varejo e para a futura plataforma SaaS multi-tenant. O sistema precisa responder, de maneira auditável, a quatro perguntas diferentes:

1. **Quem é a pessoa?** (`Employee`/colaborador, proprietário, contador etc.)
2. **Qual conta/sessão está autenticada?** (`User`/identidade digital)
3. **Em qual organização e loja ela está operando?** (`OrganizationMembership`, `StoreScope`)
4. **O que ela pode fazer neste recurso específico?** (autorização por ação + objeto + escopo)

Autenticação, autorização, autorização por objeto e isolamento de tenant são controles distintos.

## Usuário não é funcionário

`User` e `Employee` são relacionados, mas não são a mesma entidade.

### `User`

Representa a identidade usada para acessar o Mora Core.

Campos conceituais:

```text
User
├── id
├── email/login
├── credentialStatus
├── mfaStatus
├── lastLoginAt
├── disabledAt?
└── security metadata
```

### `Employee`

Representa a pessoa vinculada à operação comercial.

```text
Employee
├── id
├── organizationId
├── displayName
├── primaryStoreId?
├── employmentStatus
├── admissionDate?
├── terminationDate?
├── commissionPlanAssignment?
└── metadata operacional mínima
```

Motivos para separar:

- uma pessoa pode existir como colaboradora sem acesso ao sistema;
- um contador pode ter acesso sem ser vendedor;
- uma conta pode ser desativada sem destruir o histórico laboral/comercial;
- histórico de vendas e comissões precisa continuar íntegro após desligamento;
- no SaaS, a mesma identidade pode participar de mais de uma organização.

**Nunca apagar um funcionário apenas porque saiu da empresa.** O padrão é inativar/encerrar vínculo, preservando referências históricas.

## Membership e escopo

A identidade autenticada não escolhe livremente `organizationId` no payload. O backend resolve organizações autorizadas a partir das memberships da sessão.

```text
User
  ↓
OrganizationMembership
  ↓
RoleAssignment / StoreScope
  ↓
Resource authorization
```

Exemplo:

```text
Ana
└── Organization: Mora
    ├── SELLER  → Loja Feminina
    └── CASHIER → Loja Feminina

Maria
└── Organization: Mora
    └── MANAGER → Loja Feminina + Loja Masculina
```

## Papéis iniciais

Papéis são ponto de partida, não substituem permissões específicas.

| Papel | Responsabilidade típica |
| --- | --- |
| `OWNER` | administração da organização, segurança, configuração e visão consolidada |
| `ADMIN` | operação administrativa delegada |
| `MANAGER` | vendas, estoque, relatórios, aprovações e fechamentos dentro do escopo |
| `CASHIER` | recebimento, caixa, abertura/fechamento e operações permitidas |
| `SELLER` | venda, consulta de produto/estoque, métricas próprias quando permitido |
| `STOCK_OPERATOR` | recebimento, inventário, transferência e ajustes autorizados |
| `ACCOUNTANT` | acesso fiscal/financeiro explicitamente concedido |
| `VIEWER` | leitura limitada |

Uma pessoa pode acumular papéis. Não espalhar `if role == ...` pela aplicação; resolver capabilities/permissões em um módulo de autorização.

## Permissões e ações sensíveis

Exemplos de capabilities:

```text
sale.create
sale.cancel
sale.discount.apply
sale.discount.approve
cash.open
cash.close
cash.withdraw
cash.supply
inventory.receive
inventory.adjust
inventory.transfer
commission.view.self
commission.view.organization
commission.configure
employee.manage
product.publish
integration.manage
```

A decisão de autorização deve considerar:

- organização;
- loja/stock location;
- recurso;
- ação;
- owner do recurso quando aplicável;
- papel/capability;
- estado atual do domínio;
- política de aprovação.

## Vendedor e caixa são responsabilidades diferentes

Uma venda pode ter:

```text
sellerId  = quem realizou/atribuiu a venda
cashierId = quem recebeu/processou o caixa
```

No varejo simples podem coincidir, mas o domínio não deve assumir isso. Isso evita atribuição incorreta de comissão quando uma pessoa atende e outra finaliza o pagamento.

Evolução possível, somente se a operação real exigir: atribuição por item (`SaleItemSellerAssignment`) para vendas compartilhadas.

## Aprovação de ações de risco

Ações como desconto elevado, cancelamento, sangria, ajuste de estoque e alteração de regra de comissão podem exigir autorização adicional.

Exemplo conceitual:

```text
SELLER solicita desconto de 20%
        ↓
Policy avalia limite
        ↓
MANAGER approval requerido
        ↓
Approval registrado
        ↓
Venda prossegue
```

A aprovação deve registrar:

- quem solicitou;
- quem aprovou;
- ação e recurso;
- valor/limite relevante;
- motivo quando exigido;
- timestamp;
- request/correlation ID.

Não registrar senha/PIN em log. Se houver step-up authentication, registrar apenas o resultado e o método.

## Sessões e segurança da conta

Baseline futuro:

- credenciais com hash forte quando houver senha;
- MFA para perfis privilegiados quando aplicável;
- sessões revogáveis;
- logout global/por dispositivo;
- rotação/revogação de tokens;
- proteção contra brute force/credential stuffing;
- rate limits;
- cookies/tokens com configuração segura conforme cliente;
- eventos de segurança auditáveis;
- recuperação de conta com controles anti-takeover.

Passkeys podem ser avaliadas em ADR futuro; não são requisito obrigatório da primeira versão.

## Desligamento e mudança de função

Mudança de cargo/permissão não reescreve o passado.

Exemplo:

```text
Employee.active = false
User.disabled = true
historical Sale.sellerId continua válido
historical CommissionEntry continua válido
```

A remoção de acesso deve ser imediata ou compatível com a política de revogação definida. Jobs e sessões já emitidas não podem manter privilégios indefinidamente.

## Auditoria

Ações relevantes precisam de `AuditEvent` append-only ou equivalente, protegido contra alteração casual.

Campos conceituais:

```text
AuditEvent
├── id
├── organizationId
├── actorUserId
├── employeeId?
├── storeId?
├── action
├── resourceType
├── resourceId
├── before? (sanitizado)
├── after?  (sanitizado)
├── reason?
├── approvalId?
├── requestId
├── device/session metadata limitada
└── occurredAt
```

Nunca registrar secrets, dados de cartão ou payloads sensíveis indiscriminadamente.

## Isolamento SaaS

Este documento deve ser lido junto de [Multi-tenancy](../saas/multitenancy.md). Toda autorização relevante inclui a fronteira da organização. A existência de um ID válido de outro tenant nunca concede acesso.

Testes obrigatórios quando houver implementação:

- usuário da organização A não lê/escreve recurso de B;
- role válida na loja X não permite loja Y sem scope;
- IDs enumeráveis não atravessam tenant;
- background jobs preservam tenant context;
- export/admin/support respeitam boundary;
- cache não vaza recursos entre tenants.

## Privacidade e minimização

Guardar apenas dados pessoais necessários à finalidade operacional. Dados trabalhistas sensíveis, folha e documentos pessoais não entram automaticamente no escopo do ERP só porque existe `Employee`.

## Decisões pendentes

Antes da implementação final devem existir ADR/spec para:

- provedor/estratégia de autenticação;
- MFA/passkeys;
- modelo de capability/permission;
- política de suporte/impersonation SaaS, se existir;
- retenção de audit logs;
- política real de aprovação por valor/percentual;
- campos de funcionário necessários à operação real.

## Documentos relacionados

- [Vendas, caixa e comissões](sales-cash-commissions.md)
- [Multi-tenancy](../saas/multitenancy.md)
- [Segurança](../security/security-architecture.md)
- [Aplicativo mobile](../mobile/mobile-app.md)
- [Engineering Constitution](../engineering/constitution.md)
