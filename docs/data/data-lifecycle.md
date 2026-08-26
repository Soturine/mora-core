# Data Lifecycle, Retenção, Backup e Recuperação

> **Status:** política técnica planejada. Prazos legais/contratuais finais precisam de validação antes do SaaS público.

## Objetivo

Garantir que dados sejam criados, usados, derivados, exportados, arquivados e removidos de forma consistente em todas as suas representações — não apenas na tabela principal.

## Classificação

### Autoritativo persistente

Exemplos:

- vendas;
- movimentos de estoque;
- caixa;
- comissão;
- catálogo;
- usuários/memberships;
- configurações;
- mappings de integração.

### Derivado persistente

- `InventoryBalance`;
- thumbnails;
- WebP/AVIF;
- relatórios materializados;
- search index;
- storefront projection.

Deve ser reconstruível ou ter estratégia de recuperação.

### Cache

- CDN;
- response cache;
- query cache.

Não é fonte de verdade.

### Temporário

- upload em processamento;
- export temporário;
- arquivos intermediários;
- job scratch data.

Tem expiração.

## Representações de um dado

Ao falar “deletar produto/cliente/tenant”, mapear:

```text
PostgreSQL
Object Storage
Derived media
CDN/cache
Search index
Analytics/reporting
Exports
Logs/audit
Integration providers
Backups
```

Deletar row não resolve lifecycle inteiro.

## Proveniência

Dados vindos de integração/import/IA precisam registrar origem suficiente:

- source/provider;
- external ID;
- import/job ID;
- timestamp;
- schema/version;
- actor;
- transformações.

Isso ajuda reconciliation e auditoria.

## Dados financeiros/históricos

Vendas, comissão, caixa e estoque podem possuir necessidade de retenção e integridade histórica. “Direito de exclusão” não deve ser interpretado sem considerar obrigações legais.

Definir política com apoio jurídico/contábil antes do SaaS público.

## PII

Classificar por finalidade:

- identidade de usuário;
- funcionário;
- cliente;
- fiscal;
- suporte;
- analytics.

Aplicar minimização e acesso por necessidade.

## Fotos

### Original

Preservado e imutável por processamento comum.

### Derivados

Referenciam `sourceAssetId`, transformação e versão.

### IA

Registrar provider/model/template e aprovação quando aplicável.

Ao excluir original, decidir lifecycle dos derivados e CDN.

## Retenção

Cada categoria precisa de tabela de política futura:

```text
DataCategory
purpose
retention
legalBasis/contract
owner
deletionProcedure
backupTreatment
```

Não colocar prazo arbitrário sem base.

## Soft delete vs archive vs delete

- **archive/deactivate:** recurso deixa uso operacional mas histórico permanece;
- **soft delete:** cuidado: frequentemente vira dado “fantasma” sem policy;
- **hard delete:** quando permitido e necessário.

Escolher por domínio.

Funcionário desligado: normalmente inativar, preservando referências históricas.

Produto antigo: arquivar, não apagar vendas.

## Tenant offboarding

Fluxo:

```text
request autorizado
→ export
→ revoke sessions/tokens
→ disconnect integrations
→ unpublish storefronts
→ apply retention policy
→ delete derived/cache
→ schedule authoritative deletion where legal
→ backup expiry according to policy
→ evidence/report
```

## Backup

Backups automáticos, criptografados e separados da aplicação principal.

Para produção PostgreSQL, avaliar:

- snapshots;
- PITR/WAL;
- retenção;
- região/conta separada;
- proteção contra deleção/ransomware.

Object storage também precisa de versioning/lifecycle quando necessário.

## Restore

Backup sem restore testado não é evidência.

Exercício:

```text
criar ambiente isolado
→ restaurar DB
→ restaurar/reconectar mídia
→ aplicar/validar migrations
→ subir versão compatível
→ executar smoke real
→ verificar invariantes
→ medir RPO/RTO obtidos
```

## RPO/RTO

Não inventar números antes de negócio/produção. Antes do go-live, proprietários precisam concordar com:

- quanto dado pode ser perdido (`RPO`);
- quanto tempo pode ficar indisponível (`RTO`).

Resultados de exercícios devem validar metas.

## Exportabilidade

Cliente SaaS consegue obter dados próprios em formato documentado, conforme permissões/retention.

Possíveis exports:

- produtos;
- variantes;
- estoque/movimentos;
- vendas;
- clientes;
- fornecedores;
- comissão;
- mídia manifest;
- mappings relevantes.

Arquivos de export expiram e usam URL assinada.

## Caches

Mudança/deleção relevante precisa invalidar cache/CDN. Dados pessoais não devem ficar em cache público.

## Search/analytics

Se introduzidos, lifecycle precisa incluir reindex/delete/retention. Não criar data lake “para usar depois” sem finalidade.

## Logs

Logs têm retenção própria e redaction. Não usar log como banco de PII.

Audit logs têm necessidades diferentes e acesso mais restrito.

## Secrets

Secret lifecycle é separado:

- criação;
- versionamento;
- uso;
- rotação;
- revogação;
- destruição;
- audit.

Nunca entram em backup/export de tenant em texto aberto.

## Migrations

Mudança de schema/data precisa preservar lifecycle e rollback/recovery. Backfill idempotente e observável.

## Testes

- restore;
- export/import round-trip quando aplicável;
- deleção de derived assets;
- cache invalidation;
- offboarding staging;
- tenant isolation no export;
- backup encryption/access;
- media provenance.

## Relacionados

- [Import/export](import-export-migration.md)
- [Incidentes/DR](../operations/incident-recovery.md)
- [Privacidade/LGPD](../security/privacy-lgpd.md)
- [Mídia/IA](../ai/media-pipeline.md)
