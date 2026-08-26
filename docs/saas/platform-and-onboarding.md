# Plataforma SaaS e Onboarding

> **Status:** arquitetura de produto planejada. Billing, preços e automações concretas ainda dependem de ADR/implementação.

## Objetivo

Permitir que um comércio comece a usar o Mora Core com o mínimo de intervenção manual da equipe da plataforma, preservando segurança, consistência e possibilidade de migração.

## Princípio

Hoje a Mora é o primeiro design partner. Amanhã, outro comércio deve conseguir entrar sem existir código especial como:

```text
if organization == "Mora"
```

Capacidades especiais devem ser configurações/entitlements reutilizáveis.

## Unidade de tenancy

`Organization` é a fronteira primária do cliente SaaS.

Uma organização pode conter:

```text
Organization
├── LegalEntity(s)
├── Brand(s)
├── Store(s)
├── StockLocation(s)
├── Employee(s)
├── Website(s)
├── SalesChannel(s)
└── Integration(s)
```

Ver [Multi-tenancy](multitenancy.md).

## Onboarding self-service

Fluxo de referência:

```text
Criar conta
→ verificar identidade/email
→ criar organização
→ cadastrar estrutura legal/marcas
→ criar loja(s)/estoque(s)
→ escolher origem do catálogo
   ├── importar Bling
   ├── CSV/Excel
   └── cadastrar manualmente
→ convidar equipe
→ configurar permissões
→ configurar comissão quando aplicável
→ conectar integrações/canais
→ criar site opcional
→ checklist de prontidão
→ go-live
```

Cada etapa possui estado e pode ser retomada.

## Provisionamento

Provisionamento precisa ser idempotente e compensável.

Não pode ocorrer:

- organização criada sem owner;
- site órfão;
- billing criado sem tenant;
- integração vinculada ao tenant errado;
- retry criando lojas duplicadas.

Possível state machine:

```text
CREATED
→ SETUP_IN_PROGRESS
→ READY_FOR_VALIDATION
→ ACTIVE
→ SUSPENDED
→ CLOSED
```

Semântica final depende do produto.

## Importação

Cliente existente dificilmente quer redigitar milhares de produtos.

Caminhos:

- Bling;
- CSV/Excel;
- Olist/Tiny/Omie ou outros adapters futuros;
- cadastro manual.

Pipeline:

```text
upload/connect
→ inspect
→ mapping
→ preview
→ dedupe
→ dry-run
→ confirm
→ job idempotente
→ reconciliation/report
```

## Convites

Convite de equipe deve:

- estar ligado a organization;
- carregar role/escopo proposto;
- expirar;
- ser de uso único;
- não aceitar tenant diferente;
- permitir revogação;
- gerar audit.

## Configuração inicial por perfil

Wizard pode perguntar de forma simples:

- quantas lojas;
- departamentos;
- se usa código de barras;
- se paga comissão;
- se precisa de site;
- se vende em marketplaces;
- sistema atual;
- meios de pagamento.

O wizard não deve esconder configurações avançadas, mas progressive disclosure reduz complexidade.

## Site no onboarding

Cliente sem site pode:

```text
selecionar marca
→ logo/cores
→ tema
→ contatos
→ homepage
→ catálogo
→ subdomínio
→ preview
→ publicar
→ conectar domínio próprio depois
```

DNS/TLS têm estados e erros explícitos.

## Integrações

Cada organização conecta sua própria conta:

```text
Tenant
→ Connect Marketplace/ERP
→ OAuth/authorization
→ callback server-side
→ state/tenant validation
→ credential vault
→ health test
```

Nunca usar uma credencial global compartilhada para contas distintas sem necessidade/contrato.

## Checklist de go-live

Dependendo das features habilitadas:

- owner válido;
- lojas/localizações;
- usuários/permissões;
- catálogo mínimo;
- estoque reconciliado;
- fiscal configurado se PDV ativo;
- pagamentos configurados se necessário;
- backups/restore de plataforma;
- integrações saudáveis;
- site/domínio quando usado;
- política de comissão quando usada;
- treinamento/aceite.

## Trials

Trial é capability de billing/entitlements, não estado espalhado pelo domínio.

Durante trial, dados continuam tenant-scoped e protegidos igual a cliente pagante.

## Suspensão

Falha de pagamento não pode simplesmente destruir operação ou dados.

Política futura deve definir:

- grace period;
- funções read-only;
- bloqueio de novas operações;
- acesso a export;
- retenção;
- reativação.

Nunca corromper estoque/caixa porque subscription expirou.

## Offboarding

Fluxo:

```text
solicitação autorizada
→ validar pendências/retention
→ exportar dados
→ revogar integrações/tokens
→ despublicar sites quando cabível
→ bloquear sessões
→ lifecycle de dados
→ deleção/retention conforme contrato/lei
```

Excluir tenant não é um único `DELETE`.

## Suporte

SaaS público exige:

- status de tenant;
- integration health;
- jobs;
- audit;
- diagnósticos sem expor secrets;
- runbooks;
- comunicação de incidente.

Qualquer impersonation futuro exige step-up, justificativa, tempo limitado e audit detalhado.

## Portabilidade

Cliente deve conseguir sair com seus dados em formatos documentados, respeitando retenção legal.

Isso reduz barreira de adoção e lock-in agressivo.

## Métricas de onboarding

- tempo até organização ativa;
- tempo de import;
- erros por etapa;
- percentual que conclui onboarding;
- tempo até primeiro produto/venda;
- suporte necessário;
- taxa de integração bem-sucedida.

## Segurança

- tenant context desde criação;
- invite abuse protection;
- rate limiting;
- anti-automation quando necessário;
- email/domain verification conforme feature;
- secrets somente backend;
- authorization fail-closed;
- audit.

## Testes

- retry de criação;
- falha parcial;
- convite expirado/reutilizado;
- tenant mismatch;
- import duplicado;
- domain provisioning;
- subscription state;
- offboarding;
- export autorizado;
- cross-tenant adversarial.

## Relacionados

- [Multi-tenancy](multitenancy.md)
- [Billing](billing-entitlements.md)
- [Import/export](../data/import-export-migration.md)
- [Storefront](../product/storefront.md)
