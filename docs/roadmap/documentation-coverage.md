# Cobertura da Documentação

## Estado da auditoria

Em 2026-08-26 foi feita uma segunda auditoria de cobertura usando os quatro textos consolidados da conversa sobre:

1. funcionários, login, vendas, comissão e fechamento de caixa;
2. aplicativo mobile, scanner, estoque, fotos e IA;
3. PIM/OMS, marketplaces, modelo virtual e publicação multicanal;
4. arquitetura SaaS, multi-tenancy, sites, cloud, segurança e operação.

A auditoria concluiu que a primeira versão do repositório possuía **boa cobertura temática, porém profundidade insuficiente em vários documentos**. Em vez de declarar “está tudo” com base apenas na existência de arquivos, os documentos centrais foram aprofundados e foi criada uma [matriz de rastreabilidade](requirements-traceability.md).

## O que agora está documentado de forma explícita

- `User` separado de `Employee`;
- memberships, roles/capabilities e escopo por loja;
- seller diferente de cashier;
- commission plan/ledger/statement;
- devolução/cancelamento e reversão;
- caixa, movimentos, fechamento cego, sangria e suprimento;
- aprovação de desconto/cancelamento;
- mobile como ferramenta operacional;
- cadastro/variantes/fotos pelo celular;
- barcode/GTIN/SKU interno/etiqueta/QR e RFID futuro;
- inventário como contagem + ajuste auditado;
- offline/outbox/idempotência;
- Product Truth vs Generated Content;
- fotos originais imutáveis e derivados;
- modelo virtual por IA com proveniência;
- AI Gateway, quotas e evals;
- storefront/site builder seguro;
- categorias dinâmicas, página de produto e zoom;
- Novidades, Mais Vendidos e Destaques como conceitos distintos;
- PIM/OMS, ChannelListing, adapters e Compliance Engine;
- TikTok Shop, Mercado Livre e Shopee como integrações planejadas;
- overselling/reservas/safety stock/reconciliation;
- Organization/LegalEntity/Brand/Store/StockLocation/Website;
- PostgreSQL multi-tenant inicial e RLS como defesa adicional possível;
- onboarding, billing, entitlements e feature flags;
- import/export/migração;
- object storage e secrets;
- QA, DevSecOps, SRE/AIOps, performance, recovery e runbooks;
- arquitetura por capabilities, sem exceções `if organization == Mora`.

## Cobertura não é implementação

Todos os itens acima permanecem `planejados` enquanto não houver código/evidência correspondente. A matriz prova que a ideia está documentada; não prova que existe em produção.

## Deliberadamente não finalizado

Os tópicos abaixo **não devem ser artificialmente fechados em documentação** porque dependem de fatos ainda não levantados, decisões arquiteturais futuras ou contratos externos mutáveis:

### Discovery da operação
- política real de comissão;
- cálculo sobre bruto/líquido/desconto;
- regra de trocas/devoluções;
- limites reais de descontos, cancelamentos e sangrias;
- fechamento por turno ou diário;
- número/configuração de caixas;
- fluxo real de compra/fornecedor/recebimento;
- topologia de CNPJs, depósitos e lojas;
- hardware atual;
- conectividade/contingência necessária.

### Fiscal/pagamentos
- modelo final NFC-e/NF-e por operação;
- provedor fiscal;
- TEF/adquirentes/Pix;
- política de conciliação;
- integração com contabilidade/folha.

### Stack e infraestrutura
- framework backend final;
- ORM/query layer;
- auth provider/implementação;
- cloud/provider;
- job/queue technology;
- object storage provider;
- observability backend;
- estratégia de deploy;
- decisão de monorepo.

Esses itens precisam de ADR quando decididos.

### Contratos externos
- endpoints/scopes atuais de TikTok Shop;
- endpoints/scopes atuais de Mercado Livre;
- acesso/contrato atual da Shopee Open Platform;
- requisitos de imagem/IA/listing por categoria;
- quotas/rate limits atuais;
- política fiscal/fulfillment vigente;
- Bling API/webhooks vigentes no momento da integração.

Toda integração precisa revalidar documentação oficial antes de implementação.

### SRE/segurança/legal
- SLOs numéricos;
- RPO/RTO;
- capacidade/budgets medidos;
- política de retenção final;
- DPA/Termos/Política de Privacidade;
- processo formal LGPD;
- pentest independente;
- runbooks executáveis;
- processo de suporte/impersonation SaaS.

### Produto/SaaS
- preços e nomes dos planos;
- quotas comerciais finais;
- UX/design system final;
- cobrança/billing provider;
- domínio/subdomínio comercial do site builder;
- estratégia de checkout próprio;
- priorização final das integrações.

## Regra de completude

Um documento só deve ser chamado de “completo para implementação” quando tiver, conforme o risco:

- requisito/objetivo;
- atores e permissões;
- invariantes;
- estados/transições;
- modelo/contratos;
- erros e edge cases;
- concorrência/idempotência;
- segurança/privacidade;
- observabilidade;
- critérios de aceitação;
- estratégia de testes;
- decisões/ADRs necessárias;
- dependências externas validadas.

Antes disso, use `parcial`, `planejado` ou `não validado`.

## Fonte de rastreabilidade

Veja [Matriz de Rastreabilidade dos Requisitos Discutidos](requirements-traceability.md) para a relação tema → documento canônico.
