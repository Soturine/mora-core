# Cenário de Referência — Como as Lojas Mora Operariam com o Mora Core Completo

> **Objetivo:** mostrar, em linguagem de negócio e com um exemplo concreto, como todas as capacidades planejadas do Mora Core se conectam quando a plataforma estiver madura.
>
> **Importante:** este é um **cenário futuro ilustrativo baseado nas operações reais das lojas Mora**, não uma afirmação de que todos esses processos já existem hoje, nem de que preços, produtos, percentuais, CNPJs, políticas ou fornecedores abaixo sejam fatos atuais. As regras definitivas dependem do [Discovery Operacional](../discovery/operational-discovery.md).

## 1. A Mora como organização de referência

No estado-alvo, o Mora Core pode representar a operação assim:

```text
Organization: Mora
│
├── LegalEntity(s)                  ← CNPJ/topologia ainda depende de Discovery
│
├── Brand / operação feminina e familiar
│   ├── Store: loja feminina/familiar
│   ├── StockLocation(s)
│   ├── Website
│   └── canais digitais próprios
│
├── Brand / operação masculina
│   ├── Store: loja masculina
│   ├── StockLocation(s)
│   ├── Website
│   └── canais digitais próprios
│
├── Employees / Users
├── Suppliers
├── Catalog
├── Inventory
├── Sales / Cash / Commissions
├── WhatsApp / Conversational Commerce
├── Marketplace Connections
└── Reporting / Audit
```

As duas operações servem como **design partner inicial** do produto. O sistema, porém, nunca assume que “Mora” é o único cliente possível: em um futuro SaaS, cada comércio será outra `Organization` isolada.

## 2. Quem usa o sistema

### Proprietário

Pode acompanhar, conforme política:

- vendas por loja e consolidadas;
- estoque;
- compras;
- fornecedores;
- caixas;
- comissões;
- canais digitais;
- demanda não atendida;
- integrações;
- configurações da organização;
- auditoria e saúde operacional.

### Gerente

Pode administrar a(s) loja(s) autorizada(s), aprovar exceções e acompanhar operação diária.

### Vendedora

Pode, conforme permissão:

- consultar produto/estoque;
- atender clientes;
- registrar vendas;
- acompanhar suas próprias vendas;
- ver peças vendidas;
- acompanhar comissão provisionada/estimada;
- usar o assistente de IA como copiloto.

Não recebe automaticamente custo, margem, comissão de colegas ou administração global.

### Caixa

Opera caixa, recebimento e fechamento de acordo com permissões.

### Estoque/compras

Recebe mercadoria, faz inventário, transferências, etiquetas, pedidos de compra e sourcing.

### Contador

Recebe apenas o acesso fiscal/contábil explicitamente necessário.

---

# 3. Um dia completo na Mora — do fornecedor ao cliente

## 3.1 Antes da compra: o sistema ajuda a decidir o que procurar

Antes de uma ida a fornecedores, o proprietário pode abrir uma visão como:

```text
COMPRAS / REPOSIÇÃO

Estoque baixo                         18 variantes
Produtos com alto giro              12
Produtos sem estoque e alta procura  9
Pedidos de clientes não atendidos   27
Solicitações de sourcing             8
Produtos parados                     14
```

A recomendação pode combinar, quando houver dados confiáveis:

- vendas reais;
- giro;
- aging;
- estoque atual;
- estoque reservado;
- compras em trânsito;
- demanda perdida;
- pedidos explícitos de clientes;
- lead time;
- margem/custo autorizado;
- sazonalidade;
- orçamento.

O sistema **não compra sozinho porque uma IA sugeriu**. Uma pessoa aprova o que entra no plano de compra.

## 3.2 Viagem de compras

Exemplo: a família programa uma ida a São Paulo.

No Mora Mobile:

```text
ProcurementTrip
“Compras — São Paulo”

Reposições planejadas                  22
Pedidos específicos de clientes         6
Itens para pesquisar                    11
Budget autorizado                 R$ ...
```

Um pedido de cliente pode aparecer como:

```text
Vestido semelhante à referência enviada
Tamanho: M
Cor desejada: preto
Preço máximo informado: R$ ...
Referência: [foto]
Status: procurar candidato
```

Se encontrarem algo:

1. fotografam o candidato;
2. registram fornecedor/custo/detalhes necessários;
3. o Mora Core pode enviar uma opção para a cliente;
4. a cliente aprova ou recusa;
5. a compra só é confirmada de acordo com a política da loja.

Interesse não vira obrigação de compra automaticamente.

---

# 4. A mercadoria chega

Ao receber uma remessa, a pessoa usa o app.

## Produto com código do fabricante

```text
câmera lê barcode
→ Mora Core procura identificador
→ produto/variante existente?
   ├── sim → abre recebimento
   └── não → inicia cadastro
```

## Produto sem código

Mora Core pode gerar:

- `variantId` interno;
- SKU interno;
- código de barras interno, por exemplo Code 128;
- etiqueta.

Não inventa EAN/GTIN oficial.

## Grade de moda

Exemplo ilustrativo:

```text
Produto: Vestido Modelo A

           P   M   G
Preto      ✓   ✓   ✓
Azul       ✓   ✓   —
```

Cada combinação válida é uma `ProductVariant` com identificação e saldo próprios.

## Entrada de estoque

O recebimento gera movimentos auditáveis:

```text
PurchaseReceipt
→ InventoryMovement(RECEIPT)
→ Balance atualizado
```

O saldo não é “digitado por cima”.

---

# 5. Fotos, mídia e IA

A pessoa tira fotos pelo celular.

```text
foto original
→ upload
→ validação
→ object storage
→ processamento assíncrono
→ derivados web/thumb
→ revisão
```

A original fica preservada.

A IA pode ajudar a criar:

- melhor enquadramento;
- correção de iluminação;
- fundo limpo/branco;
- thumbnails;
- título;
- descrição;
- tags;
- categoria sugerida;
- conteúdo por canal;
- imagem com modelo virtual quando permitido.

## Regra de fidelidade

Se o produto real é preto, manga curta e possui determinada estampa, a IA não pode transformá-lo em outra cor, manga, tecido ou detalhe apenas para a imagem “ficar bonita”.

```text
Product Truth
≠
Generated Content
```

Conteúdo gerado possui proveniência e, onde necessário, aprovação humana.

---

# 6. Produto fica pronto para publicação

Depois da revisão:

```text
DRAFT
→ READY
→ PUBLISHED
```

A mesma fonte canônica pode alimentar:

- site feminino/familiar;
- site masculino;
- WhatsApp;
- TikTok Shop;
- Mercado Livre;
- Shopee;
- futuros canais.

Cada canal recebe uma projeção/listing próprio. A descrição ou imagem pode variar por canal, mas os fatos essenciais permanecem vinculados ao produto canônico.

## Novidades

Produto novo não significa “arquivo editado hoje”. A regra usa publicação/primeira disponibilidade apropriada para não transformar importação de produto antigo em novidade falsa.

## Mais vendidos

É calculado com linhas de vendas reais concluídas, nunca pela quantidade de estoque existente.

---

# 7. Cliente entra no site da Mora

Exemplo ilustrativo:

```text
Home
→ Feminino
→ Vestidos
→ produto
→ Preto / M
```

Na página de produto ela vê:

- fotos;
- galeria;
- zoom;
- descrição;
- preço verificado ou consulta quando necessário;
- variantes possíveis;
- disponibilidade permitida;
- CTA de compra/WhatsApp;
- produtos relacionados da própria organização.

O site consome uma projeção pública do Mora Core e **nunca recebe custo, fornecedor, tokens ou notas internas**.

---

# 8. Cliente quer comprar pelo WhatsApp

Ela toca:

```text
[ Comprar pelo WhatsApp ]
```

A conversa já chega contextualizada com produto/variante.

O assistente pergunta naturalmente, mas consulta tools determinísticas:

```text
getProduct
getValidVariants
getCurrentPrice
getAvailability
```

Exemplo:

> “O preto M está disponível. Quer retirar na loja ou prefere entrega?”

A IA não inventa preço nem saldo.

## Cross-sell

Pode perguntar:

> “Quer que eu mostre uma bolsa ou acessório que combine?”

Somente com produtos reais da mesma `Organization`.

---

# 9. Se o produto acabou

Suponha que o `Preto / M` tenha saldo disponível zero.

O fluxo tenta salvar a venda:

```text
mesma variante em outro StockLocation da Mora
→ outra variante compatível
→ produto semelhante da Mora
→ listing oficial da Mora em outro canal
→ back-in-stock
→ CustomerRequest
→ sourcing
→ handoff humano
```

## Regra absoluta

**Nunca recomendar um concorrente ou produto de outra `Organization` do SaaS.**

Mesmo busca visual, embeddings, caches e agentes precisam respeitar o tenant boundary antes de rankear candidatos.

---

# 10. Cliente envia uma foto

Ela manda:

> “Vocês têm algo assim?”

Fluxo:

```text
imagem externa
→ validação de mídia
→ extração visual/embedding
→ filtros
→ Catalog Search tenant-scoped
→ disponibilidade real
```

Se houver produtos semelhantes, são mostrados.

Se não houver:

> “Quer que eu registre esse estilo para a equipe avaliar na próxima compra?”

Isso cria `CustomerRequest` e `DemandSignal`, não um produto fictício.

---

# 11. Carrinho, reserva e última unidade

Quando a compra avança:

```text
Cart
→ Reservation
```

Para uma última unidade:

```text
physical = 1
reserved = 1
available = 0
```

Reserva tem TTL e regras de expiração. Se o cliente abandonar e a reserva expirar, a peça volta a ficar disponível.

Duas pessoas tentando a última unidade são resolvidas por transação/constraint/lock apropriado, não pela velocidade do chatbot.

---

# 12. Pagamento

O Mora Core pode trabalhar com adapters para:

- Pix;
- link de pagamento;
- adquirente/TEF;
- recursos do canal quando elegíveis;
- pagamento na retirada, se política permitir.

## Comprovante

Cliente mandar screenshot não significa `PAID`.

A fonte preferida é:

```text
provider webhook assinado
ou
query idempotente ao provider
```

Sem integração, entra em `AWAITING_MANUAL_REVIEW`.

---

# 13. Venda online confirmada

Quando pagamento/condição de conclusão estiver válida:

```text
Order
→ Sale
→ InventoryMovement(SALE)
→ CommissionEntry? conforme policy
→ Fiscal job
→ Fulfillment
```

Todo retry precisa ser idempotente para não:

- duplicar cobrança;
- duplicar venda;
- baixar estoque duas vezes;
- emitir documento duas vezes.

---

# 14. Fiscal

Na fase inicial, o caminho mais seguro pode ser:

```text
Mora Core
→ Bling / provedor fiscal já validado
→ SEFAZ
```

Depois:

```text
Mora Core
→ FiscalPort
→ provider especializado ou integração apropriada
```

NF-e/NFC-e dependem da operação e regras vigentes. NFS-e é para serviços e não substitui nota de mercadoria.

O Core registra status, chave/referência, XML/representação quando aplicável e reconcilia estados ambíguos.

---

# 15. Entrega ou retirada

## Retirada

- loja;
- prazo;
- reserva;
- status `READY_FOR_PICKUP`;
- identificação/código quando útil.

## Entrega

- endereço validado;
- cotação;
- prazo;
- fulfillment;
- tracking.

O sistema não promete frete ou prazo antes da fonte correta confirmar.

---

# 16. Venda física no PDV

Na loja, uma vendedora atende a cliente.

```text
Seller = vendedora A
Cashier = funcionária B
```

Podem ser a mesma pessoa ou não.

Fluxo:

```text
scan do SKU
→ produto/variante
→ preço
→ itens
→ desconto permitido?
→ aprovação se necessário
→ pagamentos
→ venda finalizada
→ estoque
→ caixa
→ comissão
→ fiscal
```

Uma venda finalizada não é editada silenciosamente depois.

---

# 17. Venda pelo Mercado Livre / Shopee / TikTok Shop

Pedido entra pelo adapter do canal:

```text
external order
→ verify/mapping
→ IntegrationInbox/dedupe
→ Order canônico
→ Reservation
→ pagamento/status do canal
→ Fiscal
→ Fulfillment
→ Sale/Inventory conforme lifecycle definido
→ sync de estoque aos demais canais
```

Se o WhatsApp oferecer continuar a compra em um marketplace, só pode oferecer um `ChannelListing` que pertença à própria `Organization` Mora.

---

# 18. Troca, devolução ou cancelamento

Exemplo:

```text
cliente devolve 1 item
→ Return/ReturnItem
→ estoque conforme condição física/policy
→ refund/ajuste financeiro
→ fiscal conforme regra
→ CommissionEntry de reversão quando aplicável
→ audit
```

O total mensal de comissão não é simplesmente reescrito.

---

# 19. Fechamento do caixa

No fim do turno/dia:

```text
CashSession
→ valores esperados por meio
→ operador informa contado/reconciliado
→ diferença
→ motivo/aprovação quando necessário
→ CLOSED
```

Pode existir blind close.

Movimentos como sangria e suprimento registram ator, horário, valor, motivo e aprovação quando exigida.

---

# 20. Comissão da funcionária

Durante o mês, a vendedora pode ver no app apenas o que a política permite:

```text
Minhas vendas
Minhas peças
Comissão provisionada
Reversões
Meta
```

No fechamento mensal:

```text
CommissionEntries
→ validação
→ CommissionStatement
→ APPROVED/SETTLED conforme processo
```

A regra real — bruto/líquido, desconto, devolução, categoria, meta — será configurada após Discovery e validação contábil/trabalhista adequada.

---

# 21. Dashboard dos proprietários

No estado maduro, os proprietários conseguem responder perguntas como:

### Vendas

- quanto vendeu hoje/mês;
- qual loja/canal vendeu mais;
- ticket médio;
- unidades por venda;
- performance por categoria/produto/variante.

### Funcionários

- vendas por vendedora;
- comissão provisionada/fechada;
- metas;
- descontos/cancelamentos autorizados.

### Estoque

- saldo por loja;
- baixo/zerado;
- giro;
- aging;
- sell-through;
- divergências de inventário;
- transferências.

### Compras

- pedido x recebido;
- fornecedor;
- lead time;
- custo;
- compra sugerida;
- sourcing.

### Demanda perdida

```text
Vestido preto M          23 solicitações
Calça feminina 44        17
Conjunto infantil 8      14
```

Também é possível medir quantas dessas oportunidades foram recuperadas por alternativas ou sourcing.

### Canais

- site;
- WhatsApp;
- TikTok Shop;
- Mercado Livre;
- Shopee;
- POS.

Sempre separando venda real de interesse/demanda.

---

# 22. O que acontece quando algo falha

## WhatsApp fora do ar

- pedido/estoque continuam no Mora Core;
- mensagens entram em retry/fila conforme contrato;
- funcionária pode usar outros canais/processos;
- não duplicar venda ao recuperar.

## Marketplace rejeita listing

- listing fica em erro;
- produto canônico continua válido;
- erro fica acionável;
- corrigir e republicar apenas aquele canal.

## IA indisponível

- cadastro e venda continuam manualmente;
- Product Truth não depende da IA.

## Provider fiscal indisponível

- venda/pedido entra no estado apropriado;
- contingência segue regra validada;
- reconciliation depois;
- nunca fingir autorização.

## Internet da loja cai

Capabilities offline são específicas. Inventário/mobile pode ter outbox; PDV offline só será liberado depois de estratégia fiscal/conflito testada.

---

# 23. Segurança nesse cenário

- cada pessoa tem login próprio;
- permissões por organização/loja;
- servidor resolve tenant;
- busca/IA/embeddings são tenant-scoped;
- secrets ficam no backend;
- marketplace/WhatsApp tokens não chegam ao app;
- ações críticas têm audit;
- dinheiro usa representação exata;
- transações evitam dupla baixa;
- uploads são validados;
- backups precisam ser restauráveis;
- MFA para perfis privilegiados quando aplicável.

---

# 24. Como isso vira SaaS sem misturar empresas

Quando outra loja contratar o Mora Core:

```text
Mora Core Platform
│
├── Organization: Mora
│   └── dados/lojas/canais da Mora
│
└── Organization: Cliente B
    └── dados/lojas/canais do Cliente B
```

A busca da Mora **não enxerga Cliente B**.

O WhatsApp da Mora **não recomenda Cliente B**.

Um funcionário da Mora **não abre vendas de Cliente B**.

Jobs, cache, vector search, logs operacionais e integrações também precisam preservar esse isolamento.

O novo cliente pode futuramente:

1. criar organização;
2. cadastrar/importar catálogo;
3. configurar lojas;
4. convidar equipe;
5. conectar ERP/marketplaces/WhatsApp;
6. criar site se não tiver;
7. começar a operar conforme entitlements.

---

# 25. Mapa completo do estado-alvo

```text
                         MORA CORE PLATFORM
                                  │
                    Identity / Organization / RBAC
                                  │
        ┌─────────────────────────┼──────────────────────────┐
        │                         │                          │
    ERP / Operação           Commerce / Canais          Experiência
        │                         │                          │
 Catalog / Inventory          PIM / OMS              Admin Web
 Purchasing / Sourcing       WhatsApp Agent           Mora Mobile
 Sales / Cash               Marketplaces             Mora POS
 Commissions                Storefront               Sites Mora
 Customers / CRM            Payments/Fiscal          Inbox humana
        │                         │                          │
        └─────────────────────────┼──────────────────────────┘
                                  │
                        Application / Domain Core
                                  │
           ┌──────────────────────┼───────────────────────┐
           │                      │                       │
      PostgreSQL             Object Storage          Jobs/Workers
           │                      │                       │
           └──────────────────────┼───────────────────────┘
                                  │
        Bling / Meta / TikTok / ML / Shopee / Fiscal / Payment
```

---

# 26. O que ainda não é decisão final

Mesmo neste cenário completo, permanecem abertos até Discovery/ADR/POC:

- CNPJ/topologia legal real;
- percentual e regra de comissão;
- política de reserva;
- trocas/devoluções;
- TEF/adquirente/Pix;
- provider fiscal final;
- estratégia de entrega;
- hardware;
- stack backend/ORM/auth/cloud;
- queue/jobs;
- runtime final de IA;
- Meta Business Agent vs agent próprio/híbrido;
- contratos atuais dos marketplaces;
- planos/preços do SaaS;
- RPO/RTO/SLO numéricos.

Esses itens não devem receber valores fictícios apenas para o documento parecer “completo”.

---

# 27. Como ler a documentação técnica a partir deste exemplo

| Se você quer entender... | Leia |
| --- | --- |
| módulos | [Módulos e capacidades](modules.md) |
| arquitetura | [Arquitetura do sistema](../architecture/system-architecture.md) |
| sequências técnicas | [Fluxos end-to-end](../architecture/end-to-end-flows.md) |
| produtos/variantes | [Modelo de domínio](../domain/domain-model.md) |
| estoque | [Estoque](../domain/inventory.md) |
| compras/sourcing | [Compras](../domain/purchasing-and-suppliers.md) + [Sourcing](../commerce/customer-demand-and-sourcing.md) |
| vendas/caixa/comissão | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| WhatsApp | [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md) |
| atendimento humano | [Operação de conversas](../commerce/conversation-operations.md) |
| marketplaces | [Omnichannel](../commerce/omnichannel.md) |
| fiscal | [Fiscal Brasil](../integrations/fiscal-brazil.md) |
| IA/fotos | [Pipeline de mídia](../ai/media-pipeline.md) |
| SaaS | [Multi-tenancy](../saas/multitenancy.md) |
| segurança | [Segurança](../security/security-architecture.md) |
| fases | [Roadmap](../roadmap/roadmap.md) |

---

## Resumo

No estado-alvo, o Mora Core conecta **compra → recebimento → produto → fotos/IA → estoque → site/WhatsApp/marketplaces/PDV → pagamento → fiscal → venda → caixa → comissão → analytics → nova compra**.

O mesmo núcleo serve às duas operações Mora e, futuramente, a outras organizações isoladas no SaaS — sem cadastrar o mesmo produto várias vezes e sem permitir que dados ou recomendações atravessem empresas.