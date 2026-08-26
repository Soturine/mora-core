# Cobertura da Documentação

## Estado da auditoria

Em 2026-08-26 foram feitos passes sucessivos de auditoria de cobertura usando os textos consolidados da conversa e os requisitos adicionais surgidos durante a evolução do Mora Core.

A cobertura inclui agora, de forma rastreável:

1. funcionários, login, vendas, comissão e fechamento de caixa;
2. aplicativo mobile, scanner, estoque, fotos e IA;
3. PIM/OMS, marketplaces, modelo virtual e publicação multicanal;
4. arquitetura SaaS, multi-tenancy, sites, cloud, segurança e operação;
5. WhatsApp como canal de venda com agente de IA;
6. busca visual por foto enviada pelo cliente;
7. isolamento comercial de recomendações por `Organization`;
8. demanda não atendida, encomendas e sourcing/viagens de compra;
9. integração entre site, WhatsApp e canais próprios de marketplace;
10. pagamento conversacional, comprovação e fiscalidade brasileira;
11. WebServices oficiais de NF-e/NFC-e e APIs nacionais de NFS-e para serviços;
12. pesquisa/benchmark de commerce conversacional e Meta Business Agent.

A primeira versão do repositório possuía boa cobertura temática, porém profundidade insuficiente em vários documentos. Os documentos centrais foram aprofundados e a [matriz de rastreabilidade](requirements-traceability.md) é a prova de cobertura tema → fonte canônica.

## O que agora está documentado de forma explícita

### Pessoas, vendas e caixa

- `User` separado de `Employee`;
- memberships, roles/capabilities e escopo por loja;
- seller diferente de cashier;
- commission plan/ledger/statement;
- devolução/cancelamento e reversão;
- caixa, movimentos, fechamento cego, sangria e suprimento;
- aprovação de desconto/cancelamento.

### Mobile, catálogo e estoque

- mobile como ferramenta operacional;
- cadastro/variantes/fotos pelo celular;
- barcode/GTIN/SKU interno/etiqueta/QR e RFID futuro;
- inventário como contagem + ajuste auditado;
- offline/outbox/idempotência;
- estoque por ledger e reservas.

### IA e mídia

- Product Truth vs Generated Content;
- fotos originais imutáveis e derivados;
- modelo virtual por IA com proveniência;
- AI Gateway, quotas e evals;
- descrição/classificação assistidas;
- busca visual;
- IA não autoritativa para preço/estoque/pagamento/fiscal/tenant.

### Storefront e commerce

- storefront/site builder seguro;
- categorias dinâmicas, página de produto e zoom;
- Novidades, Mais Vendidos e Destaques distintos;
- PIM/OMS, ChannelListing, adapters e Compliance Engine;
- TikTok Shop, Mercado Livre e Shopee como integrações planejadas;
- WhatsApp como canal transacional planejado;
- site → WhatsApp contextualizado;
- carrinho, reserva, handoff e pós-venda;
- overselling/safety stock/reconciliation.

### Isolamento comercial SaaS

- `Organization/LegalEntity/Brand/Store/StockLocation/Website`;
- PostgreSQL multi-tenant inicial e RLS como defesa adicional possível;
- recommendation/search/vector search tenant-scoped;
- proibição explícita de recomendar concorrente/outro tenant;
- marketplace só é alternativa ao cliente quando listing/connection pertence à mesma `Organization`;
- testes adversariais para busca, cache, embeddings e agent tools.

### Demanda e sourcing

- `CustomerRequest`;
- `DemandSignal`;
- interesse vs sourcing vs encomenda/pré-venda;
- `SourcingRequest`;
- `ProcurementTrip` para viagens de compra;
- `SourcingCandidate`;
- foto/preço de candidato e aprovação da cliente;
- compra → recebimento → reserva → aviso;
- agregação de demanda perdida;
- `PurchaseSuggestion` explicável;
- supplier catalog adapters futuros;
- pesquisa externa de fornecedor separada do atendimento ao consumidor.

### Fiscal/pagamento

- `Payment` separado de Sale/Cash;
- comprovante visual não confirma automaticamente;
- webhook/consulta PSP ou revisão humana;
- `FiscalPort`;
- NF-e/NFC-e via ecossistema oficial SEFAZ/MOC/WebServices;
- NFS-e padrão nacional/APIs para serviços;
- XML/DANFE;
- DARF explicitamente diferenciado de documento fiscal da venda;
- certificados/secrets;
- homologação, contingência e reconciliation;
- Bling/provedor como estratégia inicial de menor risco.

### Engenharia e operação

- onboarding, billing, entitlements e feature flags;
- import/export/migração;
- object storage e secrets;
- QA, DevSecOps, SRE/AIOps, performance, recovery e runbooks;
- arquitetura por capabilities, sem exceções `if organization == Mora`;
- `AGENTS.md` agora contém os invariantes de recommendation boundary, pagamento e sourcing.

## Documentos adicionados no passe de commerce conversacional

- `docs/commerce/whatsapp-commerce-agent.md`;
- `docs/commerce/customer-demand-and-sourcing.md`;
- `docs/integrations/fiscal-brazil.md`;
- `docs/research/conversational-commerce-benchmark.md`;
- `docs/roadmap/conversational-commerce-roadmap.md`.

Também foram atualizados Storefront, Omnichannel, Purchasing, Multi-tenancy, Fiscal/Pagamentos, README, índice, módulos, visão executiva, rastreabilidade e `AGENTS.md`.

## Cobertura não é implementação

Todos os itens permanecem `planejados` enquanto não houver código/evidência correspondente. A matriz prova que a ideia está documentada; não prova que existe em produção.

## Deliberadamente não finalizado

Os tópicos abaixo **não devem ser artificialmente fechados em documentação** porque dependem de fatos ainda não levantados, decisões arquiteturais futuras ou contratos externos mutáveis.

### Discovery da operação

- política real de comissão;
- cálculo sobre bruto/líquido/desconto;
- regra de trocas/devoluções;
- limites reais de descontos, cancelamentos e sangrias;
- fechamento por turno ou diário;
- número/configuração de caixas;
- fluxo real de compra/fornecedor/recebimento;
- frequência/forma das viagens a fornecedores;
- regra real para encomenda, sinal e reserva;
- como pedidos por foto são tratados hoje;
- número(s) e operação atual do WhatsApp;
- topologia de CNPJs, depósitos e lojas;
- hardware atual;
- conectividade/contingência necessária.

### Fiscal/pagamentos

- modelo final NFC-e/NF-e por operação;
- eventual uso de NFS-e se houver serviço;
- provedor fiscal final;
- TEF/adquirentes/Pix;
- payment provider para site/WhatsApp;
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
- decisão de monorepo;
- tecnologia de busca vetorial e desenho de isolamento, se realmente necessária.

Esses itens precisam de ADR quando decididos.

### Contratos externos

- endpoints/scopes atuais de TikTok Shop;
- endpoints/scopes atuais de Mercado Livre;
- acesso/contrato atual da Shopee Open Platform;
- WhatsApp Business Platform/App Review/templates/pricing atuais;
- disponibilidade e contrato do Meta Business Agent;
- requisitos de imagem/IA/listing por categoria;
- quotas/rate limits atuais;
- política fiscal/fulfillment vigente;
- Bling API/webhooks vigentes no momento da integração;
- WebServices/notas técnicas SEFAZ vigentes no momento do fiscal go-live.

Toda integração precisa revalidar documentação oficial antes de implementação.

### SRE/segurança/legal

- SLOs numéricos;
- RPO/RTO;
- capacidade/budgets medidos;
- política de retenção final para conversas/fotos de clientes;
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
- priorização final das integrações;
- runtime final do commerce agent;
- política STORE_ONLY/BRAND_ONLY/ORGANIZATION por cliente;
- regras de sourcing/encomenda configuráveis.

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
- multi-tenancy quando aplicável;
- observabilidade;
- critérios de aceitação;
- estratégia de testes;
- decisões/ADRs necessárias;
- dependências externas validadas.

Antes disso, use `parcial`, `planejado` ou `não validado`.

## Fonte de rastreabilidade

Veja [Matriz de Rastreabilidade dos Requisitos Discutidos](requirements-traceability.md) para a relação tema → documento canônico.
