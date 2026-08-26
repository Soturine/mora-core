# Benchmark — Commerce Conversacional, WhatsApp e Agentes de Venda

> **Status:** pesquisa estratégica atualizada em 26/08/2026. Produtos, políticas e preços da Meta e demais fornecedores mudam; revalidar antes de implementação ou claim comercial.

## Objetivo

Entender como empresas e plataformas usam WhatsApp, catálogos, Flows, IA, pagamentos e handoff humano para orientar o Mora Core sem transformar o produto numa cópia de fornecedor específico.

---

## 1. WhatsApp Business como canal comercial

O WhatsApp já suporta uma jornada que vai muito além de FAQ:

```text
conversa
→ catálogo/produto
→ descoberta
→ Flow estruturado
→ carrinho/pedido
→ pagamento quando elegível
→ suporte/pós-venda
```

A Meta posiciona o WhatsApp como ambiente no qual pessoas podem conversar com empresas, navegar por produtos, fechar compras e receber suporte.

### Aprendizado

A hipótese Mora Core “WhatsApp como primeiro checkout online” é coerente com a evolução real da plataforma.

---

## 2. Catálogo

A Meta lançou catálogos no WhatsApp Business para reduzir o envio manual repetitivo de fotos/detalhes. Cada item pode ter fotos, preço, descrição, link e código de produto.

### Caso brasileiro citado pela Meta

**Cupcakes by Isa**, de Ribeirão Preto, participou do acesso inicial a catálogo. A Meta descreveu a funcionalidade como forma de manter clientes dentro da conversa e evitar envio individual de cada produto.

### Aprendizado Mora Core

Catálogo no WhatsApp não deve virar segunda fonte de verdade.

```text
Mora Core Product Truth
→ ChannelListing(WHATSAPP)
→ catálogo WhatsApp
```

---

## 3. WhatsApp Flows

Flows permite experiências ricas/estruturadas dentro da conversa, como formulários, seleção e processos transacionais.

A Meta apresenta casos como:

- escolher assento;
- marcar compromisso;
- pedir/comprar produtos e serviços;
- feedback/product browsing.

### Casos brasileiros divulgados pela Meta

#### Banco PAN

Participou do piloto global de Flows para simplificar oferta/contratação de produtos de crédito e relatou foco em agilidade/conversão.

#### Consórcio Magalu

Usou Flow para agendamento de conversa com especialista e gestão de interações no próprio WhatsApp.

### Aprendizado Mora Core

Misturar dois modos:

```text
conversa natural
+ componentes estruturados
```

Exemplos Mora:

- escolher tamanho/cor;
- endereço;
- entrega/retirada;
- confirmar carrinho;
- solicitar troca;
- registrar interesse/encomenda.

O LLM não deve ser responsável por validar campos críticos que um Flow/schema pode validar melhor.

---

## 4. Pagamentos no Brasil

A Meta disponibilizou pagamentos para pequenas empresas no Brasil e posteriormente anunciou extensão para médias/grandes empresas via WhatsApp Business Platform, além de uma experiência mais estruturada com Pix.

### Aprendizado

Mora Core deve manter `PaymentPort` abstrato.

Não assumir que:

- todo tenant é elegível;
- todo meio está disponível;
- condições comerciais serão as mesmas quando implementarmos.

E comprovante em imagem não é autoridade de pagamento.

---

## 5. Meta Business Agent (2026)

A Meta anunciou o **Meta Business Agent** e a **Meta Business Agent Platform**.

Capacidades publicamente descritas incluem:

- responder perguntas específicas da empresa;
- recomendações a partir do catálogo;
- qualificação de leads;
- agendamento;
- fechamento de vendas;
- definição de quando equipe humana assume;
- integração com sistemas externos.

A Meta afirmou em junho de 2026 que mais de um milhão de empresas já usam um Meta Business Agent no WhatsApp/Messenger.

A plataforma também foi anunciada com conexão a sistemas como Shopify, Zendesk e Shopee.

### Implicação

“Agente comercial conectado a sistemas internos” está virando uma categoria de produto real.

### Decisão Mora Core

Não depender exclusivamente do runtime da Meta.

Arquitetura:

```text
Channel Adapter
→ Conversation Orchestrator
→ Tool/Policy Layer
→ Mora Core domains
```

Isso permite:

- usar agente próprio;
- avaliar Business Agent da Meta;
- compartilhar lógica entre site/WhatsApp/Instagram;
- manter invariantes no Core.

---

## 6. Handoff humano

A própria direção do Meta Business Agent inclui controle de quando uma pessoa entra para fornecer suporte.

### Aprendizado

Handoff não é “falha do bot”. É capacidade principal.

Mora Core precisa entregar à funcionária:

- resumo;
- intenção;
- produtos mostrados;
- disponibilidade;
- carrinho;
- pagamento/status;
- problema pendente.

---

## 7. Conversational commerce em pequenos negócios

O valor não é apenas automação. Pequenos comércios já usam WhatsApp como extensão natural da venda física.

A Meta publicou pesquisa na América Latina indicando que ferramentas como WhatsApp Business são amplamente consideradas importantes para MPMEs, com interesse elevado no uso de IA generativa.

### Hipótese Mora Core

O pequeno lojista não precisa aprender uma interface de e-commerce complexa para cada conversa. O agente pode transformar linguagem natural em operações estruturadas.

---

## 8. Omnichannel físico + digital

A Meta também vem ampliando produtos de anúncios omnichannel, inclusive mostrando loja próxima e estoque em contexto publicitário.

Isso reforça a direção:

```text
inventory truth
→ canais digitais
→ loja física
```

Estoque por loja/local é ativo comercial, não apenas backoffice.

---

## 9. Onde o Mora Core pode diferenciar

WhatsApp, catálogo, IA e pagamento isoladamente não são diferenciais exclusivos.

A tese é combinar:

```text
cliente pede por texto/foto
→ busca SOMENTE na Organization
→ disponibilidade multiloja
→ alternativas reais
→ personal shopper
→ carrinho/reserva
→ pagamento
→ fiscal
→ estoque
→ comissão/handoff
→ demanda perdida
→ sourcing/compra
→ reposição
→ cliente avisado
```

Especialmente forte é fechar o ciclo **demanda não atendida → compras**.

---

## 10. Funcionalidades recomendadas

### Conversa e descoberta

- NLU/intenção;
- texto;
- imagem;
- áudio/transcrição;
- busca visual;
- filtros naturais;
- recomendação tenant-scoped;
- comparação.

### Conversão

- carrinho;
- reserva;
- cross-sell;
- coupon/promo policy;
- entrega/retirada;
- pagamento;
- handoff.

### Recuperação de venda

- alternativa mesma variante;
- outra loja da mesma organização;
- produto similar;
- back-in-stock;
- wishlist;
- sourcing request;
- carrinho abandonado consentido.

### Pós-venda

- status;
- tracking;
- fiscal document;
- troca;
- devolução;
- suporte.

### Inteligência comercial

- demanda perdida;
- perguntas sem resposta;
- produtos mais solicitados sem estoque;
- conversão das alternativas;
- sourcing conversion;
- motivo de abandono.

---

## 11. Regras que evitam um agente ruim

- não inventar produto;
- não cruzar tenant;
- não sugerir concorrente;
- não confirmar pagamento por screenshot;
- não criar urgência falsa;
- não prometer reposição sem sourcing/PO;
- não negociar desconto fora da policy;
- não esconder handoff;
- não guardar PII/conversas para sempre;
- não usar LLM como authorization engine.

---

## 12. Build vs Buy

### Meta Business Agent

Avaliar:

- disponibilidade para nosso cenário;
- idioma/qualidade;
- integração/actions;
- pricing;
- tenancy;
- observability;
- data/privacy;
- portability;
- handoff;
- lock-in.

### Plataforma própria de agent orchestration

Avaliar:

- custo de engenharia;
- provider abstraction;
- evals;
- tooling;
- memory;
- guardrails;
- omnichannel reuse.

### Recomendação

Construir **o domínio, tool layer e contracts próprios** independentemente do runtime de IA escolhido. Assim não há lock-in arquitetural.

---

## 13. Fontes de referência

- Meta — Catálogos no WhatsApp Business: https://about.fb.com/br/news/2019/11/apresentamos-o-catalogo-para-pequenas-empresas/
- Meta — Compras/pagamentos/atendimento: https://about.fb.com/br/news/2020/10/compras-pagamentos-e-atendimento-ao-cliente-tudo-no-whatsapp/
- Meta — Pagamentos em pequenas empresas no Brasil: https://about.fb.com/br/news/2023/04/agora-voce-pode-pagar-uma-pequena-empresa-no-brasil-pelo-whatsapp/
- Meta — WhatsApp Flows: https://about.fb.com/br/news/2023/09/whatsapp-lanca-flows-recurso-que-permite-as-empresas-criarem-experiencias-nativas-no-aplicativo/
- Meta — IA e pagamentos/PIX para empresas: https://about.fb.com/br/news/2024/06/novas-ferramentas-de-ia-meta-verified-e-muito-mais-para-empresas-no-whatsapp/
- Meta — MPMEs América Latina: https://about.fb.com/br/news/2025/08/26598-pesquisa-revela-o-impacto-da-meta-nas-mpmes-da-america-latina/
- Meta — Meta Business Agent 2026: https://about.fb.com/br/news/2026/06/conversations-2026-apresentamos-o-meta-business-agent/
- WhatsApp Business Platform workspace: https://www.postman.com/meta/whatsapp-business-platform/overview/
- Embedded Signup: https://www.postman.com/meta/whatsapp-business-platform/documentation/du6gzjv/embedded-signup

## Relacionados

- [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md)
- [Demanda e Sourcing](../commerce/customer-demand-and-sourcing.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Benchmark ERP/PDV](competitive-benchmark.md)
