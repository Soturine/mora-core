# WhatsApp Commerce Agent — Assistente de Vendas Conversacional

> **Status:** arquitetura planejada e hipótese forte para a primeira etapa de vendas online. Não implica integração já implementada.
>
> **Princípio:** a IA conversa, recomenda e conduz a jornada; **Mora Core é autoridade para catálogo, variante, preço, estoque, reserva, pedido, pagamento, fiscal, venda e auditoria**.

## 1. Visão

O WhatsApp pode ser o primeiro checkout conversacional do Mora Core. O cliente encontra um produto no site, Instagram, QR code, anúncio ou diretamente no WhatsApp e conversa com um vendedor virtual capaz de usar dados reais da operação.

```text
Site / Instagram / QR / anúncio / contato direto
                    ↓
           WhatsApp Business
                    ↓
      Conversation / Channel Gateway
                    ↓
            AI Sales Agent
                    ↓
          Tool / Policy Layer
                    ↓
                 Mora Core
   ┌────────────────┼─────────────────┐
   │                │                 │
Catalog          Inventory          Commerce
Pricing          Reservations       Cart / Order
Customer         Promotions         Audit
   └────────────────┼─────────────────┘
                    ↓
       Payment / Fiscal Adapters
                    ↓
        Sale + Inventory Ledger
                    ↓
       confirmação e pós-venda
```

O objetivo não é criar um chatbot de FAQ, mas um **assistente comercial transacional** com limites, rastreabilidade e handoff humano.

---

## 2. Capacidades atuais do ecossistema WhatsApp relevantes

A Plataforma WhatsApp Business / Cloud API permite integração programática com sistemas próprios, envio e recebimento de texto e mídia e acompanhamento por webhooks.

Recursos relevantes para o Mora Core:

- mensagens de texto;
- imagens, documentos e vídeo;
- botões de resposta;
- listas;
- mensagens de produto único;
- mensagens de múltiplos produtos;
- catálogos associados à conta WhatsApp Business;
- templates aprovados;
- webhooks de mensagens/status;
- WhatsApp Flows para experiências estruturadas dentro da conversa;
- onboarding SaaS futuro por Embedded Signup;
- recursos de pagamento conforme disponibilidade, conta, região, elegibilidade e parceiro;
- Meta Business Agent / Business Agent Platform como opção futura de infraestrutura de agente.

Fontes oficiais/suportadas consultadas em 2026-08:

- https://www.postman.com/meta/whatsapp-business-platform/overview/
- https://www.postman.com/meta/whatsapp-business-platform/documentation/wlk6lh4/whatsapp-cloud-api
- https://whatsappbusiness.com/pt-br/products/business-platform-features/
- https://about.fb.com/br/news/2023/09/whatsapp-lanca-flows-recurso-que-permite-as-empresas-criarem-experiencias-nativas-no-aplicativo/
- https://about.fb.com/br/news/2026/06/conversations-2026-apresentamos-o-meta-business-agent/
- https://www.postman.com/meta/whatsapp-business-platform/documentation/du6gzjv/embedded-signup

**Regra:** capacidades, preços, templates, políticas, pagamentos e limites mudam. Revalidar documentação oficial antes de implementar.

---

## 3. Jornada básica: site → WhatsApp → venda

### 3.1 Entrada contextualizada

Na página de produto:

```text
[ Comprar pelo WhatsApp ]
```

O link/ponto de entrada pode carregar contexto assinado ou identificador seguro:

```text
productId
variantId?
page/source
campaign/referral?
```

Nunca confiar somente no texto preenchido pelo frontend. O backend reconsulta produto, preço e disponibilidade.

### 3.2 Descoberta

Exemplo:

```text
Cliente: Quero esse vestido preto M.
```

O agente consulta tools:

```text
getProduct(productId)
getValidVariants(productId)
getCurrentPrice(variantId, channel)
getAvailability(variantId, fulfillmentContext)
```

A resposta natural é produzida pela IA, mas os fatos são determinísticos.

---

## 4. Quando NÃO há estoque

Esse é um dos fluxos mais importantes do vendedor virtual.

A resposta ruim seria:

> Produto indisponível.

A resposta Mora Core deve tentar salvar a venda.

### 4.1 Estratégia de fallback ordenada

```text
variante desejada sem estoque
        ↓
1. mesma peça / outra unidade da organização
        ↓ se não houver
2. mesma peça / variantes compatíveis
        ↓
3. produtos semanticamente semelhantes
        ↓
4. alternativas no orçamento/preferências
        ↓
5. aviso de reposição / lista de interesse
        ↓
6. handoff humano se necessário
```

### 4.2 Outra loja

Exemplo:

```text
Loja Feminina: Preto M = 0
Loja Masculina/Outra unidade aplicável = N/A
Depósito/Outra loja da organização: Preto M = 1
```

O agente pode oferecer, conforme política:

- retirada na outra loja;
- transferência;
- entrega a partir da outra unidade;
- reserva da peça.

Nunca prometer transferência/retirada antes de o Inventory confirmar que essa operação é permitida.

### 4.3 Outra variante da mesma peça

Pode dizer:

> O preto M acabou, mas tenho o mesmo modelo em preto G e azul M. Quer ver?

O Mora Core retorna apenas combinações reais de `ProductVariant`.

### 4.4 Alternativas semelhantes

O recommendation engine deve buscar candidatos determinísticos antes da IA escrever a resposta.

Sinais possíveis:

```text
mesmo departamento/categoria
mesma audiência/faixa
mesmo tamanho disponível
cor próxima
faixa de preço
estilo/tags
material
ocasião
coleção
preferências consentidas
histórico de interação
estoque disponível
margem/regras comerciais permitidas
```

A IA pode interpretar intenção, por exemplo:

> “Quero algo parecido, mas mais soltinho e até R$ 150.”

Ela transforma isso em filtros e consulta o catálogo; **não inventa produtos**.

### 4.5 Mostrar fotos e modelos

O agente pode enviar:

- foto principal real;
- galeria;
- foto da variante correta;
- mensagem de produto único;
- multi-product message com alternativas;
- fotos de detalhe;
- mídia com modelo real autorizado;
- imagem `AI_ON_MODEL` aprovada e identificada conforme política do canal/empresa.

Nunca usar uma imagem gerada para representar falsamente cor, estampa, caimento ou detalhe.

### 4.6 Comparação conversacional

Exemplo:

```text
Cliente: Qual é mais soltinho?
```

Se existirem dados estruturados/medidas:

```text
compareProducts(ids)
```

A IA explica diferenças verificadas.

Se não houver medida/atributo confiável, responde que não possui aquela informação e pode chamar uma pessoa.

### 4.7 Lista de reposição

Se o cliente preferir esperar:

```text
BackInStockSubscription
├── organizationId
├── customerId/contact
├── productId
├── variantId?
├── channel = WHATSAPP
├── consent/status
├── createdAt
└── notifiedAt?
```

Quando houver entrada de estoque, uma automação pode gerar notificação respeitando consentimento e regras/template do WhatsApp.

### 4.8 Pré-venda

Só existe se a empresa conscientemente habilitar `PREORDER` para um produto/variante e houver regra de prazo/cancelamento. A IA jamais converte `OUT_OF_STOCK` em promessa de pré-venda por conta própria.

---

## 5. Vendedor virtual / personal shopper

Além de responder SKU exato, o agente pode atuar como uma espécie de personal shopper.

Exemplos:

```text
"Preciso de roupa para casamento até R$ 250"
"Minha filha tem 8 anos e quero um conjunto rosa"
"Quero um presente para bebê"
"Tem algo parecido com essa foto?"
```

Fluxo:

```text
entender intenção
→ extrair constraints
→ perguntar o necessário
→ consultar catálogo real
→ rankear candidatos
→ mostrar poucas opções boas
→ refinar
→ adicionar ao carrinho
```

Evitar perguntas desnecessárias e evitar perfis sensíveis/inferências indevidas.

---

## 6. Cross-sell e upsell responsáveis

A IA pode estimular venda sem dark pattern.

### Cross-sell

```text
vestido
→ bolsa
→ acessório
```

### Upsell

Pode apresentar uma alternativa superior quando houver benefício verificável, sem esconder opção mais barata.

### Regras

- não inventar escassez;
- não dizer “última unidade” sem dado consistente;
- não criar desconto inexistente;
- não pressionar vulnerabilidades;
- limitar quantidade de sugestões;
- respeitar opt-out de marketing;
- deixar claro quando sugestão é recomendação.

---

## 7. Catálogo e mensagens de produto

O WhatsApp Business Platform suporta single-product e multi-product messages associados a catálogo.

A arquitetura deve mapear:

```text
Product / ProductVariant
        ↓
ChannelListing(WHATSAPP)
        ↓
externalCatalogId
productRetailerId
```

Esse mapping fica no adapter de canal, não no domínio `Product`.

O catálogo apresentado no WhatsApp deve derivar da mesma Product Truth do Mora Core.

---

## 8. WhatsApp Flows

Flows podem complementar conversa livre com telas estruturadas dentro do WhatsApp.

Usos possíveis:

- selecionar tamanho/cor;
- confirmar endereço;
- escolher retirada/entrega;
- coletar dados necessários de pedido;
- escolher horário de retirada;
- confirmar itens;
- intenção de compra;
- solicitar troca/devolução;
- pesquisa pós-venda.

A IA decide quando oferecer um Flow, mas o Flow possui schema e validação determinísticos.

Exemplo:

```text
conversa livre
→ "Quer finalizar?"
→ abre Flow
→ variante
→ fulfillment
→ dados necessários
→ revisão
→ pedido
```

---

## 9. Carrinho conversacional

```text
Cart
├── id
├── organizationId
├── customerId/session
├── channel = WHATSAPP
├── status
└── expiresAt

CartItem
├── productId
├── variantId
├── quantity
├── unitPriceSnapshot
└── promotionSnapshot?
```

O agente pode:

- adicionar;
- remover;
- alterar quantidade;
- mostrar resumo;
- aplicar apenas promoções autorizadas;
- perguntar se cliente quer mais alguma coisa.

Antes de finalizar, servidor recalcula preço/benefícios válidos.

---

## 10. Reserva e prevenção de overselling

Para itens de baixa quantidade, a última peça é crítica.

Modelo conceitual:

```text
physical
- reserved
- safetyStock
= available
```

Reserva:

- transacional;
- com TTL;
- ligada ao cart/order;
- idempotente;
- liberada em expiração/cancelamento;
- convertida em baixa na finalização.

Se duas pessoas tentarem a última unidade, constraint/lock/transação decide; não o agente.

---

## 11. Pagamento

### 11.1 Opções

Dependendo da configuração e recursos disponíveis:

- pagamento elegível dentro do WhatsApp;
- Pix;
- link de pagamento;
- checkout externo seguro;
- pagamento na retirada quando permitido.

A Meta já anunciou pagamentos de empresas no WhatsApp no Brasil e expansão à Business Platform, mas integração/eligibilidade/parceiros devem ser revalidados no momento da implementação.

### 11.2 Comprovante enviado no chat

**Screenshot ou PDF de comprovante não confirma pagamento automaticamente.**

Pode ser recebido e associado ao caso, mas a fonte de verdade deve ser preferencialmente:

```text
webhook assinado do PSP
ou
consulta idempotente ao provider
```

Se for necessário suportar pagamento manual sem API, deve existir estado `AWAITING_MANUAL_REVIEW` e aprovação humana auditada.

### 11.3 Estados

```text
PENDING
AUTHORIZED
PAID
FAILED
EXPIRED
CANCELLED
PARTIALLY_REFUNDED
REFUNDED
AWAITING_MANUAL_REVIEW?
```

---

## 12. Confirmação do pagamento → venda → estoque

Fluxo seguro:

```text
PaymentConfirmed
      ↓
idempotency / transaction
      ↓
Order pago
      ↓
Sale finalizada
      ↓
InventoryMovement(SALE)
      ↓
CommissionEntry? conforme regra de atribuição
      ↓
Fiscal job
      ↓
Fulfillment
```

A operação deve ser recuperável caso fiscal ou envio falhe depois do pagamento.

Não criar “pagou → sete chamadas independentes sem estado”. Preferir workflow/saga local controlada com estados e retries idempotentes.

---

## 13. Fiscal: nota, DANFE e o que realmente faz sentido

`DARF` não é normalmente o documento emitido para o consumidor a cada venda comum de varejo.

Para o Mora Core, o fluxo relevante tende a envolver:

- **NFC-e** para determinadas operações de consumidor final presencial/retail, conforme UF e regra vigente;
- **NF-e** quando a operação exigir;
- XML autorizado;
- DANFE / representação auxiliar correspondente quando aplicável;
- cancelamento/correção/contingência conforme documento e legislação.

No Estado de São Paulo, a estratégia fiscal precisa considerar as regras vigentes em 2026 e ser validada com contador/provedor antes de produção.

O agente pode dizer ao cliente algo como:

> “Pagamento confirmado. Sua nota está sendo emitida.”

mas somente depois que o Payment domain confirmar `PAID`.

Após autorização fiscal, pode enviar o documento/atalho permitido pelo canal.

---

## 14. Entrega, retirada e fulfillment

O agente pode perguntar:

```text
Retirar na loja
Entregar
```

### Retirada

- loja;
- horário;
- reserva;
- prazo;
- código/identificação de retirada.

### Entrega

Evolução:

- endereço validado;
- cálculo de frete;
- prazo;
- courier/logística;
- tracking.

Não prometer frete/prazo que ainda não foi cotado pelo sistema.

---

## 15. Troca, devolução e pós-venda

Mesmo canal pode atender:

```text
"Quero trocar o tamanho"
"Onde está meu pedido?"
"Quero devolver"
"Me manda a nota"
```

Tools:

```text
getOrderStatus()
getEligibleReturnItems()
startReturnRequest()
getFiscalDocument()
requestHumanHandoff()
```

Políticas de troca/devolução são regras determinísticas e dependem do Discovery da operação e da legislação aplicável.

---

## 16. Handoff humano

O agente não deve tentar resolver tudo.

Triggers:

- cliente pede pessoa;
- baixa confiança;
- produto/medida desconhecida;
- exceção de pagamento;
- negociação fora de política;
- reclamação sensível;
- troca complexa;
- fiscal inconsistente;
- tentativa de ação privilegiada;
- sentimento/frustração persistente.

Handoff inclui resumo seguro:

```text
Cliente quer vestido X
Prefere preto M
Sem estoque local
Foram mostradas alternativas A/B
Carrinho tem item Y
Pagamento ainda não iniciado
```

A funcionária não precisa reler 50 mensagens.

Depois do handoff, regras definem se bot permanece silencioso até devolverem controle.

---

## 17. Memória e CRM com consentimento

O Mora Core pode lembrar informações úteis quando houver finalidade/consentimento adequado:

- tamanho preferido;
- categorias de interesse;
- loja preferida;
- histórico de pedidos;
- wishlist;
- consentimento de marketing.

Não criar perfil invasivo nem inferir informações sensíveis desnecessárias.

Separar:

```text
ConversationContext (curto prazo)
CustomerProfile (dados persistentes autorizados)
MarketingConsent
```

---

## 18. Carrinho abandonado e follow-up

Pode existir:

```text
CartAbandoned
→ espera política configurada
→ elegibilidade/consentimento/template
→ mensagem de retomada
```

Exemplo:

> “Seu carrinho com o vestido X ainda está disponível. Quer continuar?”

Não disparar spam e respeitar templates, categorias de mensagem, opt-out e políticas atuais do WhatsApp.

---

## 19. Fotos enviadas pelo cliente

Caso o cliente envie uma foto:

> “Quero algo parecido com isso.”

A IA pode gerar embeddings/classificação e consultar produtos reais.

Fluxo:

```text
imagem externa não confiável
→ validação de mídia
→ feature extraction / AI
→ filtros/sugestões
→ Catalog Search
→ produtos reais
```

A imagem não vira automaticamente mídia do catálogo.

Privacy/lifecycle precisam ser definidos.

---

## 20. Áudio e voz

Clientes frequentemente enviam áudio.

Possível fluxo:

```text
voice message
→ transcrição
→ classificação/intenção
→ agente
→ resposta em texto
```

Futuro: suporte de voz/call conforme recursos da Business Platform e política.

Transcrição é input não confiável; confirmar dado crítico como tamanho/endereço quando ambíguo.

---

## 21. Arquitetura de agente

### Orchestrator

```text
ConversationAgent
├── intent/context
├── product discovery
├── sales policy
├── tool selection
├── response generation
└── handoff
```

### Tool Layer

Cada tool tem:

- schema;
- permission;
- tenant context;
- timeout;
- resource budget;
- logging sanitizado;
- idempotência quando altera estado.

### Sem acesso genérico

Evitar:

```text
runSql(query)
callAnyUrl(url)
executeTool(name, freeformArgs)
```

Preferir tools estreitas e semânticas.

---

## 22. Nosso agente vs Meta Business Agent

Em 2026 a Meta anunciou `Meta Business Agent`, com recomendações de produtos, respostas específicas do negócio, handoff e fechamento de vendas, além da Business Agent Platform integrada a sistemas externos.

O Mora Core deve manter duas opções arquiteturais avaliáveis:

### Opção A — Mora Conversation Agent próprio

WhatsApp é canal; inteligência/orquestração é Mora Core.

**Vantagens:**

- controle de domínio;
- provider de IA substituível;
- mesma lógica em WhatsApp/site/Instagram/outros canais;
- observabilidade e regras próprias;
- integração profunda com inventory/order/fiscal.

### Opção B — Meta Business Agent como runtime/camada de experiência

Integrações/actions apontam para Mora Core.

**Vantagens potenciais:**

- infraestrutura nativa Meta;
- descoberta/distribuição;
- recursos de conversa integrados.

**Riscos:**

- disponibilidade/roadmap;
- custo;
- lock-in;
- limites de customização;
- governança/telemetria;
- integração com domínio crítico.

### Recomendação atual

**Não decidir ainda.** Criar ADR quando houver acesso real, documentação técnica e piloto. Em qualquer opção, Mora Core continua dono dos fatos e invariantes.

---

## 23. SaaS: cada cliente conecta seu WhatsApp

No futuro SaaS:

```text
Organization
→ Integrations
→ Conectar WhatsApp
→ Meta Embedded Signup
→ WABA / phone number vinculados ao tenant
```

`WhatsAppConnection` conceitual:

```text
id
organizationId
providerAccountId/WABAId
phoneNumberId
businessPhone
status
scopes
credentialRef
webhookConfig
catalogMapping
createdAt
lastHealthyAt
```

Tokens ficam criptografados no backend/secret manager.

Embedded Signup é importante para evitar configuração manual por cliente e é parte do caminho oficial para Tech Providers/Solution Partners.

---

## 24. Multi-tenant e segurança

Todo evento recebido precisa ser resolvido para uma conexão/organization autorizada antes de acessar dados.

```text
Webhook
→ verify authenticity
→ identify connection
→ organization context
→ inbox/dedupe
→ processing
```

Nunca confiar em número de telefone/texto enviado pelo cliente para selecionar tenant.

---

## 25. Webhooks e idempotência

```text
Meta/WhatsApp webhook
→ validar
→ persistir IntegrationInbox
→ dedupe event/message
→ responder rápido
→ worker
```

Eventos de message delivery/read também alimentam observabilidade, não regras financeiras.

Retries nunca podem:

- duplicar pedido;
- cobrar duas vezes;
- baixar estoque duas vezes;
- emitir nota duas vezes.

---

## 26. Observabilidade comercial

Além de infraestrutura, medir funil:

```text
conversation_started
product_viewed
recommendation_shown
variant_selected
added_to_cart
checkout_started
payment_created
payment_confirmed
order_completed
human_handoff
abandoned
```

Métricas:

- conversão por origem;
- tempo até primeira resposta;
- tempo até compra;
- abandono;
- produtos perguntados sem estoque;
- alternativas que salvam venda;
- taxa de handoff;
- receita assistida por IA;
- erro de tool;
- pagamentos ambíguos.

Não coletar conteúdo sensível desnecessário apenas para analytics.

---

## 27. Novos insights para estoque/compras

Conversas podem revelar demanda não convertida.

Exemplo:

```text
20 clientes pediram Vestido X / Preto M
estoque = 0
```

Isso vira sinal de demanda, separado de venda real.

Métricas:

```text
DemandSignal
├── product/attributes
├── store/channel
├── timestamp
├── converted?
└── reasonNotConverted
```

Pode ajudar compras/reposição, mas não deve ser contado como receita.

---

## 28. Personalização responsável

No futuro:

- “Você costuma comprar M; quer começar por opções M?”
- “A peça que você salvou voltou ao estoque.”

Somente com dados permitidos e transparência.

Não usar características sensíveis para manipulação comercial.

---

## 29. Anti-fraude e abuso

Threats:

- prompt injection;
- spam/bot;
- tentativa de desconto não autorizado;
- comprovante falso;
- replay de webhook;
- enumeração de pedido;
- phishing/falso atendente;
- abuso de cupom;
- tentativa de obter dados internos;
- arquivo malicioso.

Controles:

- tool allowlist;
- auth/object authorization;
- rate limits;
- signature validation;
- idempotency;
- media validation;
- deterministic pricing;
- payment provider truth;
- audit trail;
- human review.

---

## 30. UX da conversa

Princípios:

- respostas curtas;
- mostrar 2–5 opções, não 30 de uma vez;
- usar imagens quando ajudam;
- listas/botões/Flows para reduzir digitação;
- nunca esconder que é assistente virtual;
- permitir `falar com atendente` a qualquer momento;
- lembrar contexto dentro da sessão;
- resumir carrinho antes de cobrar;
- confirmar variante crítica;
- evitar repetir pergunta já respondida.

---

## 31. MVP proposto

### MVP 1 — concierge sem pagamento automático

```text
site → WhatsApp
catalog search
availability
alternatives
fotos
cart summary
handoff humano
```

Venda ainda é concluída por pessoa/processo atual.

### MVP 2 — pedido + Pix/link

Adicionar:

- Cart/Order;
- reserva;
- Payment Adapter;
- webhook de pagamento;
- baixa de estoque;
- confirmação.

### MVP 3 — fiscal e fulfillment

- Fiscal Adapter;
- documento;
- retirada/entrega;
- tracking/status.

### MVP 4 — automação comercial

- back-in-stock;
- abandono consentido;
- recomendação avançada;
- pós-venda;
- analytics de demanda.

### MVP 5 — SaaS

- Embedded Signup;
- número/WABA por tenant;
- templates;
- quotas;
- billing;
- suporte/observabilidade por organização.

---

## 32. Critérios de aceitação críticos

Não considerar pronto se:

- IA inventa disponibilidade/preço;
- sem estoque apenas responde “acabou” sem alternativas quando existem;
- variante inválida pode entrar no carrinho;
- comprovante visual confirma pagamento automaticamente;
- retry duplica venda/baixa;
- pagamento confirmado não é reconciliável;
- dados cross-tenant podem aparecer;
- agente pode emitir desconto fora da política;
- fiscal é chamado antes do evento correto;
- handoff perde contexto;
- cliente não consegue falar com pessoa;
- conteúdos gerados podem alterar Product Truth.

---

## 33. QA específico

### Conversas

- linguagem informal;
- erro ortográfico;
- mensagem fragmentada;
- áudio/transcrição ambígua;
- troca de intenção no meio;
- múltiplos produtos;
- cliente pede humano.

### Catálogo

- produto inexistente;
- sem imagem;
- preço desconhecido;
- variante impossível;
- última unidade;
- estoque em outra loja;
- alternativas vazias.

### Pagamento

- success;
- timeout;
- duplicate webhook;
- delayed confirmation;
- expired;
- refund;
- comprovante falso/manual.

### Segurança

- prompt injection;
- IDOR/BOLA;
- cross-tenant;
- tool injection;
- malformed webhook;
- replay;
- arquivo inválido.

### Recuperação

- worker reinicia após pagamento;
- Meta indisponível;
- provider de IA indisponível;
- Mora Core continua permitindo handoff/manual;
- fiscal indisponível entra em fila/estado conhecido conforme política.

---

## 34. Questões de Discovery

Antes de automatizar venda real:

- qual número WhatsApp é usado hoje?
- app WhatsApp Business ou Platform/API?
- quantas funcionárias atendem?
- como distribuem conversas?
- como confirmam pagamento hoje?
- Pix é conta/chave de quem?
- usam link/maquininha remota?
- entrega ou só retirada?
- política de reserva?
- tempo de reserva?
- política de troca/devolução?
- como emitem nota na venda remota hoje?
- existe catálogo no WhatsApp?
- querem automação 24/7?
- em quais casos a IA deve chamar pessoa?
- querem campanhas/back-in-stock?

Registrar respostas em [Discovery Operacional](../discovery/operational-discovery.md).

---

## 35. Documentos relacionados

- [Omnichannel / PIM / OMS](omnichannel.md)
- [Storefront](../product/storefront.md)
- [Vendas, caixa e comissões](../domain/sales-cash-commissions.md)
- [Estoque](../domain/inventory.md)
- [Fiscal e pagamentos](../domain/fiscal-and-payments.md)
- [Governança de IA](../ai/ai-governance.md)
- [Marketplaces](../integrations/marketplaces.md)
- [Confiabilidade de integrações](../integrations/reliability-patterns.md)
- [Multi-tenancy](../saas/multitenancy.md)
- [Segurança](../security/security-architecture.md)
- [Discovery Operacional](../discovery/operational-discovery.md)
