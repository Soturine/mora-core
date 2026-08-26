# Demanda de Clientes, Encomendas e Sourcing de Mercadorias

> **Status:** arquitetura planejada. Este documento modela uma capacidade importante do Mora Core: transformar pedidos que hoje se perdem em conversa (“tem algo assim?”, “quando forem para São Paulo trazem para mim?”) em demanda rastreável, compra planejada e possível venda futura.

## 1. Princípio de isolamento comercial

A recomendação feita a um cliente é **sempre limitada à mesma `Organization`**.

### Invariante

```text
customer organization context = Organization A
        ↓
search/recommendation candidates
        ↓
SOMENTE produtos, variantes, lojas, marcas e listings pertencentes à Organization A
```

O Mora Core **não pode recomendar loja concorrente, produto de outro tenant ou listing de outra organização**, mesmo que a plataforma SaaS conheça esses dados.

Isso vale para:

- WhatsApp;
- chat do site;
- busca visual;
- recomendação de IA;
- “produtos parecidos”;
- back-in-stock;
- pesquisa de disponibilidade;
- Mercado Livre/Shopee/TikTok conectados;
- qualquer future commerce agent.

### Dentro da mesma organização

O escopo pode ser configurável:

```text
STORE_ONLY
BRAND_ONLY
ORGANIZATION
```

Exemplo: um site de uma marca pode priorizar a própria marca, mas, se a política comercial permitir, oferecer item de outra marca/loja **da mesma organização**, deixando claro onde está disponível.

O padrão recomendado para o Mora Core é `ORGANIZATION`, com ranking `mesma loja → mesma marca → outra loja/marca da organização`.

---

## 2. O problema de negócio

Hoje uma cliente pode perguntar:

> “Tem esse vestido?”

> “Tem algo parecido?”

> “Quando vocês forem comprar mercadoria em São Paulo, conseguem trazer um desses?”

Se a resposta for apenas “não temos”, a loja perde:

- a venda atual;
- o contato da demanda;
- a informação sobre cor/tamanho/preço desejado;
- a chance de incluir o item na próxima compra;
- dados para entender o que clientes pedem e não encontram.

O Mora Core deve transformar isso em fluxo estruturado.

---

## 3. Busca por foto enviada pelo cliente

A cliente pode mandar uma foto no WhatsApp ou no site.

Fluxo:

```text
imagem do cliente
→ validação de mídia
→ AI/Vision extraction
→ embedding visual/atributos
→ busca TENANT-SCOPED no catálogo da Organization
→ ranking de produtos reais
```

A IA pode extrair sinais como:

- categoria;
- cor;
- estampa;
- comprimento;
- manga;
- modelagem;
- ocasião;
- estilo;
- faixa de preço quando informada pelo cliente.

Esses sinais são **sugestões**, não Product Truth.

### Busca em camadas

```text
1. match exato/forte dentro do catálogo da Organization
2. mesmo produto em outra variante disponível
3. produto semelhante na mesma loja
4. produto semelhante em outra loja/marca da mesma Organization
5. listing próprio conectado (site/ML/Shopee/TikTok), se policy permitir
6. nenhum match → criar CustomerRequest / DemandSignal
```

### Nunca

- pesquisar concorrentes para recomendar ao cliente;
- usar catálogo de outro tenant;
- revelar preço/estoque de outra organização;
- usar embeddings sem `organizationId`/tenant filter;
- transformar foto externa em mídia oficial do produto sem revisão.

---

## 4. `CustomerRequest`

Representa o pedido explícito de uma pessoa por algo que a organização ainda não consegue vender imediatamente.

```text
CustomerRequest
├── id
├── organizationId
├── storeId?
├── customerId?
├── conversationId?
├── sourceChannel
├── requestedProductId?
├── requestedVariantId?
├── sourceMediaId?
├── desiredAttributes
├── desiredQuantity
├── maxPriceCents?
├── neededBy?
├── notes
├── status
├── createdAt
└── closedAt?
```

### Status sugeridos

```text
OPEN
NEEDS_DETAILS
MATCHED_INTERNAL
WAITING_RESTOCK
CANDIDATE_FOR_SOURCING
ACCEPTED_FOR_SOURCING
QUOTED
CUSTOMER_CONFIRMED
PURCHASED
RECEIVED
RESERVED_FOR_CUSTOMER
READY_FOR_PICKUP
FULFILLED
REJECTED
CANCELLED
EXPIRED
```

A state machine final depende da operação real.

---

## 5. `DemandSignal`

Nem todo pedido vira encomenda individual. A conversa também gera sinal agregado de demanda.

```text
DemandSignal
├── organizationId
├── category/attributes
├── productId?
├── variantId?
├── channel
├── storeId?
├── customerRequestId?
├── outcome
├── occurredAt
└── convertedSaleId?
```

### Outcomes

```text
OUT_OF_STOCK
NO_MATCH
PRICE_TOO_HIGH
SIZE_UNAVAILABLE
COLOR_UNAVAILABLE
CUSTOMER_WAITING_RESTOCK
SOURCING_REQUESTED
CUSTOMER_DECLINED_ALTERNATIVES
CONVERTED_ALTERNATIVE
```

Isso permite responder futuramente:

> “O que as clientes estão pedindo e não temos?”

### Exemplo de relatório

```text
Últimos 30 dias

Vestido preto M           23 pedidos não atendidos
Calça feminina 44         17
Conjunto infantil 8 anos  14
Body RN branco            11
```

**Demanda não é venda.** Não somar esses sinais a faturamento nem “mais vendidos”.

---

## 6. Encomenda vs interesse

É importante separar três conceitos.

### Interesse

> “Se chegar, me avisa.”

Não gera obrigação de compra nem reserva financeira.

### Solicitação de sourcing

> “Quando forem para São Paulo, vejam se encontram algo assim.”

A loja aceita procurar, mas ainda pode não existir preço/fornecedor/garantia.

### Pré-venda/encomenda confirmada

Só existe depois que a empresa confirmou condições suficientes:

- item ou especificação;
- preço ou faixa aprovada;
- prazo estimado;
- depósito/pagamento quando policy exigir;
- política de cancelamento;
- aprovação humana quando necessária.

A IA **nunca converte um simples interesse em obrigação comercial**.

---

## 7. `SourcingRequest`

Conecta demanda do cliente ao processo de compras.

```text
SourcingRequest
├── id
├── organizationId
├── customerRequestId?
├── buyerEmployeeId?
├── priority
├── desiredAttributes
├── targetRetailPriceCents?
├── maxAcquisitionCostCents?
├── quantityDesired
├── supplierId?
├── procurementTripId?
├── status
├── approvedBy?
├── approvedAt?
└── timestamps
```

### Status possíveis

```text
DRAFT
REVIEW
APPROVED_TO_SEARCH
SEARCHING
FOUND_CANDIDATE
WAITING_CUSTOMER_APPROVAL
APPROVED_TO_BUY
PURCHASED
NOT_FOUND
CANCELLED
```

---

## 8. Viagem de compras / ida a São Paulo

O caso real “quando meus pais forem para São Paulo” merece uma entidade própria, sem hardcode de cidade.

## `ProcurementTrip`

```text
ProcurementTrip
├── id
├── organizationId
├── title
├── destination
├── plannedStartAt
├── plannedEndAt?
├── buyers[]
├── budgetCents?
├── status
└── notes
```

Exemplo:

```text
Viagem de compras — São Paulo — 12/09

Solicitações agrupadas:
✓ 4 vestidos pretos M
✓ 2 conjuntos infantis 8 anos
✓ 3 bodies RN brancos
✓ pedido específico da cliente Maria
```

### App durante a viagem

O Mora Mobile pode oferecer modo “Compras/Sourcing”:

- lista offline/cacheada das solicitações;
- foto de referência;
- atributos desejados;
- prioridade;
- quantidade;
- teto de custo autorizado;
- cliente vinculado, somente se necessário/permissão permitir;
- fornecedor sugerido;
- marcar `encontrado`, `não encontrado`, `ver depois`;
- fotografar candidato;
- registrar preço do fornecedor;
- registrar quantidade disponível;
- criar novo fornecedor/referência como draft.

Dados pessoais devem ser minimizados; o comprador não precisa ver mais informação da cliente do que o necessário.

---

## 9. Aprovação em tempo real durante a compra

Se encontrarem algo semelhante em São Paulo:

```text
Buyer captura foto/preço
→ SourcingCandidate
→ Mora Core
→ WhatsApp/site notification
→ cliente vê
→ “quero / não quero”
```

Exemplo natural:

> “Encontramos uma opção bem parecida com a foto que você mandou, tamanho M, por R$ 149. Quer que a gente separe para você?”

### Regra

Antes de comprar em nome de uma cliente, aplicar a policy da organização:

- aprovação explícita;
- preço máximo previamente autorizado;
- depósito quando aplicável;
- validade curta da oferta;
- estoque do fornecedor não garantido até confirmação.

Não prometer item enquanto o fornecedor ainda pode vendê-lo a outra pessoa.

---

## 10. `SourcingCandidate`

```text
SourcingCandidate
├── sourcingRequestId
├── supplierId?
├── supplierReference?
├── mediaId
├── description
├── attributes
├── acquisitionCostCents
├── proposedRetailPriceCents
├── availableQuantity?
├── expiresAt?
├── status
└── capturedBy
```

O candidato **não é Product** até ser aprovado/cadastrado.

---

## 11. Da compra ao estoque

Quando o item é comprado:

```text
SourcingRequest
→ PurchaseOrder/Ad-hoc Purchase
→ PurchaseReceipt
→ Product/Variant mapping ou cadastro
→ InventoryMovement(RECEIPT)
```

Se já existe cliente confirmado:

```text
RECEIPT
→ Reservation(customer/order)
→ notify customer
```

Assim a peça não aparece como livre para venda e é vendida para outra pessoa antes da retirada.

### Expiração de reserva

Se cliente não concluir no prazo:

- notificar conforme política;
- liberar reserva;
- peça passa ao estoque geral;
- manter histórico da solicitação.

---

## 12. Compra coletiva orientada por demanda

Demandas semelhantes podem ser agrupadas.

```text
3 clientes pediram vestido preto M
5 pediram conjunto infantil tamanho 8
```

O sistema pode sugerir:

```text
PurchaseSuggestion
├── reason = UNMET_DEMAND
├── normalizedAttributes
├── requestCount
├── historicalSales?
├── currentStock
├── pendingPurchaseQty
└── suggestedQty
```

A sugestão é apoio; comprador aprova.

Quando houver histórico confiável, combinar:

- demanda não atendida;
- vendas reais;
- giro;
- estoque atual;
- estoque em trânsito;
- lead time;
- sazonalidade;
- margem;
- budget.

Não comprar automaticamente com base em LLM.

---

## 13. Integração com fornecedores

### Etapa inicial

Cadastro simples de fornecedores e referências.

### Futuro

Se fornecedor disponibilizar API/arquivo/catálogo:

```text
SupplierCatalogAdapter
→ catálogo/custo/disponibilidade B2B
→ matching interno
```

Esse é um contexto **backoffice**, diferente de recomendação ao consumidor.

### Pesquisa externa

Se futuramente um agente de compras pesquisar fornecedores na web:

- capability separada;
- somente para perfil autorizado;
- resultados não aparecem diretamente para cliente;
- não misturar concorrente de varejo com fornecedor;
- proveniência das fontes;
- humano aprova fornecedor/produto/compra.

---

## 14. Integração com marketplaces da própria organização

Se a mesma `Organization` possui listing conectado no Mercado Livre, Shopee ou TikTok Shop, o Mora Core pode usar esses canais como **canais próprios**, não como concorrentes.

Exemplo:

```text
Cliente no WhatsApp
→ produto sem checkout direto disponível
→ existe ChannelListing Mercado Livre da própria Organization
→ policy permite redirecionar
→ agente oferece “Também temos esse item na nossa loja oficial do Mercado Livre.”
```

### Regras

- `ChannelListing.organizationId` precisa ser o mesmo;
- conta marketplace precisa ser `ChannelConnection` autorizada da organização;
- usar preço específico do canal, que pode diferir;
- não prometer frete/promoção do marketplace sem consultar o listing/contrato atual;
- nunca fazer busca global do marketplace e sugerir terceiros.

---

## 15. Integração com site

No storefront podem existir CTAs:

```text
[ Perguntar pelo WhatsApp ]
[ Encontrar algo parecido ]
[ Enviar uma foto ]
[ Avise-me quando voltar ]
[ Quero pedir este estilo ]
```

### Busca visual no site

Upload de foto → tenant-scoped visual search → produtos da organização.

### Sem resultado

> “Ainda não encontrei algo assim no nosso catálogo. Quer registrar o que você procura para a equipe avaliar na próxima compra?”

Cria `CustomerRequest` com consentimento e sem prometer disponibilidade futura.

---

## 16. Wishlist e lista de desejos

Além de sourcing explícito:

```text
Wishlist
WishlistItem
```

Pode registrar produtos existentes que a cliente quer acompanhar.

Diferença:

- wishlist = item conhecido;
- back-in-stock = variante conhecida indisponível;
- customer request = necessidade que pode não existir no catálogo;
- sourcing request = decisão da loja de procurar/comprar.

---

## 17. Ranking de alternativas

Um sistema determinístico/retrieval produz candidatos. A IA explica/ordena dentro dos limites.

Possível score:

```text
availability
+ sameProduct
+ sameCategory
+ requestedAttributes
+ sizeCompatibility
+ color/style similarity
+ priceFit
+ sameStore/brand preference
+ merchandising policy
```

### Proibido

- pagar maior comissão e esconder opções melhores sem regra transparente;
- usar margem como único critério invisível;
- recomendar indisponível como disponível;
- criar falsa urgência;
- cross-tenant candidate leakage.

---

## 18. CRM e consentimento

Para avisar depois:

- registrar canal/preferência;
- consentimento/base apropriada;
- permitir opt-out;
- respeitar políticas do WhatsApp e legislação aplicável;
- não persistir a foto do cliente indefinidamente sem finalidade.

Outbound WhatsApp iniciado pela empresa pode exigir template aprovado e está sujeito a políticas/limites da Meta; revalidar antes de implementar campanhas, reposição ou carrinho abandonado.

---

## 19. Analytics de sourcing

Dashboard futuro:

### Demanda perdida

- pedidos sem match;
- out-of-stock;
- tamanhos/cores mais procurados;
- demanda por categoria;
- demanda por loja/canal.

### Eficiência de compra

- requests aceitos;
- encontrados;
- comprados;
- convertidos;
- tempo até atendimento;
- taxa de cliente desistente;
- margem da encomenda quando apropriado;
- fornecedor que mais resolve demanda.

### Funil

```text
Request
→ Sourcing accepted
→ Candidate found
→ Customer approved
→ Purchased
→ Received
→ Sold
```

---

## 20. Notificações

Eventos úteis:

- item voltou ao estoque;
- viagem de compras próxima e solicitação ainda precisa de detalhe;
- candidato encontrado;
- preço para aprovação;
- item comprado;
- mercadoria recebida;
- pronta para retirada;
- reserva perto de expirar.

Não enviar cada evento como marketing. Classificação/template/canal depende da policy e do consentimento.

---

## 21. Segurança e multi-tenancy

### Testes obrigatórios

- foto enviada para Organization A não busca embeddings de B;
- recommendation A nunca retorna produto/listing de B;
- CustomerRequest A nunca aparece no dashboard de B;
- buyer da A não vê solicitações de B;
- marketplace listing de B não aparece como alternativa de A;
- cache/vector index é tenant-aware;
- export respeita tenant;
- analytics agregados da plataforma não reidentificam outra organização.

### Platform-level learning

Se futuramente houver modelos globais, eles **não podem** transformar dados comerciais privados de um tenant em recomendação de produto de outro tenant. Qualquer uso agregado/anônimo precisa de política, base legal/contratual e arquitetura próprias.

---

## 22. Edge cases

- cliente manda foto sem dizer tamanho;
- imagem contém vários produtos;
- item é de marca protegida/cópia;
- cliente quer peça que a loja decidiu não vender;
- prazo é impossível;
- fornecedor mudou preço;
- cliente aprovou e fornecedor acabou;
- viagem foi cancelada;
- item chegou diferente;
- item encomendado não serviu;
- cliente sumiu após compra;
- várias clientes pedem a única unidade;
- pedido duplicado em WhatsApp e site.

Cada caso deve terminar em estado explícito, não nota solta em chat.

---

## 23. MVP recomendado

### MVP A

- `CustomerRequest`;
- back-in-stock;
- foto → busca interna;
- alternativas somente da organização;
- dashboard de demanda não atendida;
- handoff humano.

### MVP B

- `SourcingRequest`;
- lista de compra/viagem;
- candidato encontrado;
- aprovação da cliente;
- receiving/reservation.

### MVP C

- supplier mapping;
- purchase suggestions;
- agrupamento de demanda;
- procurement mobile offline;
- analytics de sourcing.

### MVP D

- supplier catalog adapters;
- forecasting assistido por dados;
- automações de compra, sempre com limites/aprovação proporcionais.

---

## 24. Critérios de aceitação

Não considerar pronto se:

- qualquer recomendação atravessa organization;
- “não temos” encerra fluxo quando há alternativa interna útil;
- IA inventa produto externo;
- sourcing é apresentado como compra garantida sem aprovação;
- pedido do cliente não possui status/auditoria;
- item recebido para cliente pode ser vendido livremente antes da reserva;
- foto do cliente fica armazenada sem lifecycle;
- demanda perdida é confundida com venda;
- listing de concorrente aparece por busca global.

---

## 25. Documentos relacionados

- [WhatsApp Commerce Agent](whatsapp-commerce-agent.md)
- [Omnichannel / PIM / OMS](omnichannel.md)
- [Compras e fornecedores](../domain/purchasing-and-suppliers.md)
- [Estoque](../domain/inventory.md)
- [Storefront](../product/storefront.md)
- [Mobile](../mobile/mobile-app.md)
- [Multi-tenancy](../saas/multitenancy.md)
- [Governança de IA](../ai/ai-governance.md)
- [Privacidade/LGPD](../security/privacy-lgpd.md)
