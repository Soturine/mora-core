# Cobertura da Documentação

## Estado da auditoria

Em 2026-08-26 foram feitos passes sucessivos de auditoria de cobertura usando os textos consolidados da conversa e os requisitos adicionais surgidos durante a evolução do Mora Core.

Em 05/09/2026 a documentação recebeu um novo passe baseado em **evidência de campo das duas lojas Mora**, cobrindo os PDVs atuais, computadores, leitores, impressoras, fluxos de caixa observados e sinais de qualidade do catálogo legado.

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
12. pesquisa/benchmark de commerce conversacional e Meta Business Agent;
13. baseline de hardware/PDV real das duas lojas;
14. arquitetura de integração do POS com periféricos locais;
15. estratégia de migração dos legados NOOVA/Lips/Bling com staging e reconciliação;
16. riscos operacionais de endpoints antigos, drivers, periféricos e dados legados.

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

### PDV e hardware real

O levantamento de 05/09/2026 documentou, com sanitização de PII:

- loja feminina/familiar usando NOOVA Tecnologia;
- loja masculina usando Lips Control;
- versão de PDV observada `4.01.00.134` em ambas;
- PCs, SO, processador e RAM das estações observadas;
- leitores YHD-8200 e Knup KP-1026;
- impressoras Bematech MP-4200 TH e VERDE DYJ-5801;
- diferenças de hardware entre as lojas;
- fluxos NOOVA observados de venda, formas de pagamento, sangria, suprimento, cancelamento, cliente, produtos, NFC-e, TEF e retaguarda;
- riscos de ciclo de vida de SO e endpoint security;
- necessidade de POC do futuro Mora POS nas duas estações.

Fonte canônica: [Baseline real de PDV e hardware](../discovery/mora-pos-hardware-baseline-2026-09-05.md).

### Arquitetura de periféricos

Está documentado que:

- domínio trabalha com capabilities, não marcas;
- scanner HID/keyboard wedge é caminho preferido quando confirmado;
- entrada manual é fallback obrigatório;
- impressão é side effect separado da finalização de venda;
- reimpressão não refaz venda;
- impressora térmica não é autoridade fiscal;
- `Local Device Bridge` é opção arquitetural estreita e segura, não backend paralelo;
- WebUSB/Web Serial não são dependência exclusiva para funções críticas sem matriz comprovada;
- PWA+bridge, wrapper e desktop dedicado exigem ADR/POC;
- atualização, rollback, least privilege e health de dispositivo fazem parte do desenho.

Fonte canônica: [Integração POS/periféricos](../architecture/pos-device-integration.md).

### Migração e qualidade de dados legados

A documentação agora cobre explicitamente:

- NOOVA, Lips e Bling como possíveis fontes com ownership ainda a mapear;
- extração suportada antes de scraping/engenharia reversa;
- snapshot bruto imutável;
- staging;
- provenance;
- anomaly classification;
- saldo negativo/fracionário sem correção silenciosa;
- `Sem Categoria` como dívida a mapear, não taxonomia final;
- zero vs unknown em custo/preço;
- reconstrução de produto/variante;
- dry-run;
- reconciliação;
- shadow/dual-run;
- cutover com fallback.

Fonte canônica: [Migração dos legados Mora](../data/mora-legacy-data-migration.md).

### Engenharia e operação

- onboarding, billing, entitlements e feature flags;
- import/export/migração;
- object storage e secrets;
- QA, DevSecOps, SRE/AIOps, performance, recovery e runbooks;
- arquitetura por capabilities, sem exceções `if organization == Mora`;
- `AGENTS.md` contém invariantes de recommendation boundary, pagamento, sourcing e, após o passe de campo, orientação para POS/periféricos.

## Documentos adicionados no passe de commerce conversacional

- `docs/commerce/whatsapp-commerce-agent.md`;
- `docs/commerce/customer-demand-and-sourcing.md`;
- `docs/integrations/fiscal-brazil.md`;
- `docs/research/conversational-commerce-benchmark.md`;
- `docs/roadmap/conversational-commerce-roadmap.md`.

## Documentos adicionados no passe de operação física de 05/09/2026

- `docs/discovery/mora-pos-hardware-baseline-2026-09-05.md`;
- `docs/architecture/pos-device-integration.md`;
- `docs/data/mora-legacy-data-migration.md`.

Também foram atualizados índice, riscos/questões abertas, arquitetura, QA, módulos/AGENTS quando aplicável e rastreabilidade para refletir a evidência real.

## Cobertura não é implementação

Todos os itens permanecem `planejados` enquanto não houver código/evidência correspondente. A matriz prova que a ideia está documentada; não prova que existe em produção.

O baseline de campo prova que determinado equipamento/tela foi observado, **não** que sua API, driver, contrato, fluxo fiscal ou integração TEF estejam conhecidos.

## Deliberadamente não finalizado

Os tópicos abaixo **não devem ser artificialmente fechados em documentação** porque dependem de fatos ainda não levantados, decisões arquiteturais futuras ou contratos externos mutáveis.

### Discovery da operação

- política real de comissão;
- cálculo sobre bruto/líquido/desconto;
- regra de trocas/devoluções;
- limites reais de descontos, cancelamentos e sangrias;
- fechamento por turno ou diário;
- número/configuração completa de caixas;
- fluxo real de compra/fornecedor/recebimento;
- frequência/forma das viagens a fornecedores;
- regra real para encomenda, sinal e reserva;
- como pedidos por foto são tratados hoje;
- número(s) e operação atual do WhatsApp;
- topologia de CNPJs, depósitos e lojas;
- ownership real de NOOVA/Lips/Bling por capability;
- conectividade/contingência necessária.

### Hardware — baseline parcial já conhecido

Não está mais correto dizer simplesmente “hardware atual desconhecido”. Já existe baseline real dos dois caixas. Permanecem abertos:

- drivers, portas e VID/PID;
- modo HID/keyboard wedge dos leitores;
- ligação/configuração das gavetas;
- impressora de etiquetas, se distinta;
- adquirentes/modelos de terminais;
- TEF/Pix real;
- rede/Wi-Fi/link backup;
- nobreak;
- equipamentos reserva;
- lifecycle de PCs/SO;
- requisito mínimo final do Mora POS.

### Fiscal/pagamentos

- modelo final NFC-e/NF-e por operação;
- eventual uso de NFS-e se houver serviço;
- provedor fiscal final;
- TEF/adquirentes/Pix;
- payment provider para site/WhatsApp;
- política de conciliação;
- integração com contabilidade/folha;
- relação atual entre NOOVA/Lips/Bling/SEFAZ.

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
- tecnologia de busca vetorial e desenho de isolamento, se realmente necessária;
- runtime do cliente POS;
- estratégia final de Local Device Bridge/wrapper;
- updater/distribuição do software local.

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
- APIs/exportações/portabilidade de NOOVA e Lips;
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
- processo de suporte/impersonation SaaS;
- política de hardening/renovação das estações físicas.

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
