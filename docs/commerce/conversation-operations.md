# Operação de Conversas — Inbox, Handoff, Roteamento e Identidade do Cliente

> **Status:** arquitetura planejada. Complementa o WhatsApp Commerce Agent com a parte operacional necessária para várias funcionárias atenderem clientes sem colisão, perda de contexto ou acesso indevido.

## Objetivo

Transformar WhatsApp/chat em um canal operacional real, não em “um bot solto”.

```text
Mensagem do cliente
→ Conversation
→ AI triage/assist
→ routing
→ AI / humana / híbrido
→ commerce tools
→ histórico + audit
```

## 1. `Conversation`

```text
Conversation
├── id
├── organizationId
├── channel
├── channelConnectionId
├── externalThreadId
├── customerId?
├── storeContextId?
├── status
├── mode
├── priority
├── assignedEmployeeId?
├── lastCustomerMessageAt
├── lastResponseAt?
├── createdAt
└── closedAt?
```

### Status

```text
OPEN
WAITING_AI
WAITING_HUMAN
ASSIGNED
WAITING_CUSTOMER
RESOLVED
CLOSED
```

Final state machine depende do canal.

### Mode

```text
AI
HUMAN
HYBRID
PAUSED_AI
```

## 2. Inbox compartilhada

Funcionárias autorizadas podem ver filas como:

```text
Novas
Precisa de humana
Minhas conversas
Aguardando cliente
Pagamento pendente
Problema no pedido
Encomendas/sourcing
```

Cada fila é tenant/store scoped.

## 3. Atribuição

Estratégias possíveis:

- manual;
- round-robin;
- loja de origem;
- skill/category;
- vendedora que já atendia o cliente;
- carga atual;
- horário/turno.

Não implementar algoritmo sofisticado antes de observar operação real.

## 4. Evitar duas pessoas responderem ao mesmo tempo

Quando uma conversa é assumida:

```text
ConversationAssignment
├── conversationId
├── employeeId
├── assignedAt
├── releasedAt?
└── reason
```

UI mostra ownership atual. Lock pode ser otimista/lease-like, sem impedir supervisor de transferir.

## 5. Transferência

Exemplos:

```text
Loja Feminina → Caixa
Vendedora → Gerente
IA → Humana
Humana → IA
Loja A → Loja B da MESMA Organization
```

Transferência registra motivo e preserva histórico.

Nunca transferir conversa para organização concorrente/outro tenant.

## 6. Handoff IA → humana

O agente produz resumo estruturado e verificável:

```text
intent
products discussed
selected variants
availability checked
alternatives shown
cart
reservation
payment status
customer request/sourcing
unanswered questions
handoff reason
```

Resumo é ajuda, não substituto do histórico.

## 7. Handoff humana → IA

Depois de resolver uma exceção, a funcionária pode devolver ao modo automático.

Exemplo:

```text
Gerente aprova desconto
→ AI retoma checkout
```

A IA só usa a decisão registrada pelo policy engine; não interpreta texto livre “pode dar desconto” como autorização permanente.

## 8. Assistente para a funcionária

Mesmo quando o cliente está falando com uma humana, a IA pode trabalhar em modo copiloto:

- sugerir resposta;
- buscar produto;
- comparar variantes;
- resumir histórico;
- mostrar estoque;
- sugerir alternativa;
- preparar carrinho;
- explicar política;
- encontrar pedido;
- gerar rascunho de pós-venda.

A funcionária aprova a mensagem quando o modo exige.

## 9. SLA e prioridade

Medir:

- first response time;
- customer wait time;
- human queue age;
- resolution time;
- abandoned conversation;
- handoff rate.

Prioridade pode subir por:

- pagamento ambíguo;
- cliente na loja esperando;
- pedido próximo de envio;
- reclamação;
- sourcing com oferta expirando.

Não usar IA para inferir “cliente valioso” de forma opaca e discriminatória.

## 10. Horário de atendimento

Fora do horário:

- IA pode continuar funções permitidas 24/7;
- exceções que exigem pessoa ficam em fila;
- informar prazo realista;
- não fingir que humano está online.

## 11. Identidade do cliente

Uma pessoa pode aparecer como:

```text
WhatsApp phone
site session
email
marketplace buyer reference
POS customer
```

Não fazer merge automático agressivo.

### `CustomerIdentity`

```text
customerId
organizationId
provider
externalId
verifiedAt?
```

Merge sensível precisa de evidência e possibilidade de correção.

## 12. Customer 360 proporcional

Quando permitido, atendimento pode ver:

- pedidos;
- reservas;
- wishlist;
- back-in-stock;
- customer requests;
- preferências declaradas;
- consentimentos.

Não mostrar dado de outra organização nem PII desnecessária.

## 13. Mensagens

### `ConversationMessage`

```text
id
organizationId
conversationId
direction
senderType
senderId?
externalMessageId
contentType
text?
mediaAssetId?
status
sentAt
receivedAt?
```

### Tipos

- text;
- image;
- audio;
- document;
- interactive;
- product;
- template;
- system event.

## 14. Retenção

Mensagem não deve ser guardada para sempre por padrão.

Definir:

- necessidade comercial/legal;
- janela de retenção;
- PII;
- imagens do cliente;
- documentos;
- transcripts de áudio;
- direito de export/delete quando aplicável;
- backups.

Audit de venda/pagamento não depende de manter todo chat indefinidamente.

## 15. Consentimento e outbound

Separar:

```text
service conversation
transactional notification
marketing consent
```

Template/categoria/janela/política de WhatsApp devem ser revalidados no momento da implementação.

Opt-out precisa ser respeitado.

## 16. Handoff entre canais

Possível futuro:

```text
site chat
→ WhatsApp
```

ou

```text
Instagram
→ WhatsApp
```

Preservar contexto com identificadores seguros e consentimento, sem depender de copiar texto manualmente.

## 17. Relação com comissão

Uma conversa pode ter vários atendentes, mas isso não define automaticamente comissão.

Guardar eventos de atendimento separados da regra comercial.

```text
ConversationAssignment
≠
Sale.sellerId
```

A policy pode decidir quando a venda é atribuída à funcionária.

## 18. Segurança

- conversation lookup tenant-scoped;
- employee store scope;
- media access assinado;
- PII minimizada;
- agent tools autorizadas;
- system prompt não contém secrets;
- external message ID não autoriza recurso;
- links/documentos tratados como não confiáveis;
- anti-spam/rate limit;
- audit de export/transfer/admin access.

## 19. Observabilidade

Técnico:

- webhook latency;
- inbox lag;
- AI latency;
- tool errors;
- message send failure;
- template rejection;
- provider health.

Negócio:

- conversations → carts;
- carts → paid;
- recovered sale;
- demand signal;
- sourcing request;
- human assist rate;
- conversion por mode `AI/HUMAN/HYBRID`.

## 20. QA

Testar:

- duas vendedoras tentando assumir;
- transferência;
- IA fala enquanto humana assumiu;
- cliente manda mensagens rápidas fragmentadas;
- message duplicate/out-of-order;
- handoff perde contexto;
- tenant A tenta abrir conversation B;
- funcionário sem store scope;
- timeout do WhatsApp;
- replay webhook;
- attachment inválido;
- cliente solicita humano;
- encerramento/reabertura.

## 21. Discovery

Ver [Discovery de Commerce Conversacional](../discovery/conversational-commerce-discovery.md) para decidir distribuição real do atendimento.

## Relacionados

- [WhatsApp Commerce Agent](whatsapp-commerce-agent.md)
- [Demanda e Sourcing](customer-demand-and-sourcing.md)
- [Identidade e permissões](../domain/identity-employees-permissions.md)
- [Privacidade/LGPD](../security/privacy-lgpd.md)
- [Analytics](../product/analytics-and-reporting.md)
