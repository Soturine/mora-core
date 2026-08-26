# Benchmark — Commerce Conversacional, WhatsApp e Agentes de Venda

> **Status:** pesquisa estratégica atualizada em 26/08/2026. Produtos, políticas, preços e resultados de fornecedores mudam; revalidar antes de implementação ou claim comercial. Métricas de cases são fornecidas/publicadas pelos próprios fornecedores e clientes citados, portanto servem como benchmark, não como promessa de resultado para o Mora Core.

## Objetivo

Entender como empresas e plataformas usam WhatsApp, catálogos, Flows, IA, pagamentos, substituição de ruptura, checkout e handoff humano para orientar o Mora Core sem transformar o produto numa cópia de fornecedor específico.

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

A hipótese Mora Core “WhatsApp como primeiro checkout online” é coerente com a evolução real do canal.

## 2. Catálogo — caso Cupcakes by Isa

A Meta lançou catálogos no WhatsApp Business para reduzir o envio manual repetitivo de fotos/detalhes. **Cupcakes by Isa**, de Ribeirão Preto, participou do acesso inicial citado pela Meta.

Aprendizado:

```text
Mora Core Product Truth
→ ChannelListing(WHATSAPP)
→ catálogo WhatsApp
```

O catálogo do canal não vira segunda fonte de verdade.

## 3. WhatsApp Flows — Banco PAN e Consórcio Magalu

Flows permite experiências ricas/estruturadas dentro da conversa. A Meta citou no Brasil o Banco PAN e o Consórcio Magalu em usos de jornada estruturada.

Para Mora Core, combinar:

```text
conversa natural
+ componentes estruturados
```

Exemplos: selecionar tamanho/cor, endereço, retirada/entrega, confirmar carrinho, troca e registro de interesse/encomenda.

## 4. Pagamentos no Brasil

A Meta disponibilizou pagamentos para pequenas empresas no Brasil e anunciou evolução da experiência para a Business Platform, inclusive Pix em cenários suportados.

Mora Core mantém `PaymentPort` abstrato e não assume elegibilidade, provedor ou regra futura.

Comprovante em imagem não é autoridade de pagamento.

## 5. Meta Business Agent (2026)

A Meta anunciou o **Meta Business Agent** e a **Meta Business Agent Platform** com capacidades publicamente descritas como:

- responder perguntas específicas da empresa;
- recomendar produtos do catálogo;
- qualificar leads;
- agendar;
- fechar vendas;
- definir handoff humano;
- integrar sistemas externos.

A Meta afirmou em junho de 2026 que mais de um milhão de empresas já usam um Meta Business Agent no WhatsApp/Messenger e anunciou conexões com plataformas como Shopify, Zendesk e Shopee.

### Decisão Mora Core

Não depender exclusivamente do runtime da Meta.

```text
Channel Adapter
→ Conversation Orchestrator
→ Tool/Policy Layer
→ Mora Core domains
```

Assim é possível avaliar runtime próprio, Meta Business Agent ou solução híbrida sem entregar Product Truth e invariantes ao fornecedor de IA.

## 6. VTEX WhatsApp Store

A oferta atual da VTEX posiciona uma jornada de commerce conversacional que conecta catálogo, disponibilidade, assistência por IA, carrinho, checkout, frete e pagamento no WhatsApp.

O benchmark é particularmente relevante porque mostra que “catálogo → conversa → checkout” já é uma categoria comercial madura no varejo.

Aprendizados para Mora Core:

- catálogo e estoque em tempo real são pré-requisitos;
- conversa deve estar conectada ao OMS/checkout, não a uma FAQ separada;
- preço/promoção precisam vir do commerce engine;
- UX conversacional não elimina necessidade de transações determinísticas.

Fonte: https://www.vtex.com/pt-br/solutions/business-needs/whatsapp-store/

## 7. Cencosud Brasil + Weni/VTEX — substituição por ruptura

Case publicado pela Weni/VTEX: a Cencosud Brasil automatizou via WhatsApp a aprovação de substituições quando um item do pedido não estava disponível.

Fluxo descrito:

```text
ruptura no picking
→ separador indica alternativas disponíveis
→ cliente recebe opções no WhatsApp
→ cliente escolhe
→ decisão volta ao sistema logístico
```

O case reporta aumento de faturamento e alta satisfação após a implantação.

### Por que isso importa muito para o Mora Core

É evidência de mercado para exatamente um dos fluxos que queremos:

```text
produto indisponível
→ alternativas reais
→ cliente escolhe
→ operação continua
```

Diferença Mora Core: nossa busca precisa ser estritamente limitada à mesma `Organization` e também pode acontecer **antes da compra**, não só durante picking.

Fonte: https://weni.ai/pt/casos-de-sucesso/case-cencosud

## 8. Osklen + Weni/VTEX — moda e recuperação de carrinho

A Osklen, marca brasileira de moda, publicou case com Weni/VTEX envolvendo agentes no WhatsApp para conectar jornadas como recuperação de carrinho e atendimento/pós-venda.

Esse caso é especialmente relevante porque Mora Core nasce no varejo de moda.

Aprendizados:

- WhatsApp pode ser canal de receita, não apenas SAC;
- abandono de carrinho é um evento de commerce que pode alimentar conversa contextual;
- moda se beneficia de proximidade, recomendação e conteúdo visual;
- métricas de conversão precisam ser rastreadas por canal/campanha.

Fonte: https://weni.ai/pt/casos-de-sucesso/osklen-aumenta-conversao-de-carrinhos-abandonados-em-quase-15-e-gera-r-453-mil-de-receita-incremental-em-30-dias

## 9. Carajás Home Center — recuperação e pós-venda

Case publicado pela VTEX/Weni descreve WhatsApp integrado ao e-commerce para:

- recuperação de carrinhos;
- atualização e consulta de pedidos;
- segunda via de boletos/Pix;
- acesso a notas fiscais;
- atendimento humano opcional.

Isso reforça a tese de um mesmo canal atender pré-venda, conversão e pós-venda.

Fonte: https://vtex.com/pt-br/commerce-executive-stories/carajas-alcanca-15-vezes-mais-conversao-no-whatsapp-com-weni-by-vtex

## 10. Grupo CVLB — IA integrada a sistemas existentes

Case de Casa & Vídeo/Le Biscuit divulgado por VTEX/Weni descreve agentes no WhatsApp integrados a VTEX, SAP e parceiros logísticos para resolver tarefas como:

- segunda via;
- notas fiscais;
- cancelamento/reembolso;
- tracking/status;
- troca e instalação.

O ponto mais importante não é a porcentagem reportada de automação, mas a arquitetura: **o agente executa tarefas porque está conectado aos sistemas de verdade**.

Isso valida a direção Mora:

```text
IA
→ tool layer
→ domínios/APIs reais
```

não:

```text
IA
→ improvisa resposta
```

Fonte: https://www.vtex.com/pt-br/commerce-executive-stories/grupo-cvlb-automatiza-75-do-atendimento-e-reduz-custos-em-mais-de-20-com-experiencia-conversacional/

## 11. Midea — concierge de catálogo

A VTEX descreve o uso de IA como concierge digital no WhatsApp, consultando catálogo/SKUs em tempo real, respondendo dúvidas e conduzindo ao checkout.

Para Mora Core, isso reforça o “personal shopper” especialmente em categorias onde o cliente não sabe o SKU exato.

Fonte: https://www.vtex.com/pt-br/casos-de-clientes/midea-scales-high-consideration-sales-with-agentic-cx

## 12. Blip — benchmark de plataforma de atendimento/vendas

A Blip oferece WhatsApp para varejo com automação + atendimento humano, histórico de conversa, recuperação de carrinho, status de pedido e publicação em múltiplos canais.

### Aprendizados

- vários atendentes em um número é requisito operacional real;
- histórico/handoff importa tanto quanto IA;
- a experiência deve suportar atendimento humano sem perder contexto;
- omnichannel conversacional pode incluir Instagram/Messenger.

Fonte: https://digital.blip.ai/varejo/whatsapp/

## 13. O que esses casos NÃO provam

Eles não provam que:

- o Mora Core terá os mesmos resultados;
- toda venda deve ser automatizada;
- toda empresa quer checkout dentro do WhatsApp;
- usar IA substitui discovery/processo;
- um fornecedor específico é a escolha correta.

Servem para validar padrões e hipóteses.

## 14. Handoff humano

Handoff não é “falha do bot”. É uma capability principal.

Mora Core precisa entregar à funcionária um resumo seguro de intenção, produtos mostrados, disponibilidade, carrinho, pagamento/status e pendência.

## 15. Conversational commerce em pequenos negócios

A Meta publicou pesquisa na América Latina indicando forte importância de WhatsApp Business para MPMEs e interesse elevado em IA generativa.

A hipótese Mora Core é permitir que pequeno lojista use linguagem natural sem perder estrutura transacional, segurança e auditabilidade.

## 16. Onde o Mora Core pode diferenciar

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

O ciclo **demanda não atendida → compras/sourcing** é uma hipótese particularmente diferenciadora para o pequeno/médio varejo.

## 17. Funcionalidades recomendadas

### Conversa e descoberta

Texto, imagem, áudio/transcrição, busca visual, filtros naturais, recomendação tenant-scoped e comparação.

### Conversão

Carrinho, reserva, cross-sell, policy de promoção, entrega/retirada, pagamento e handoff.

### Recuperação de venda

Outra unidade da mesma organização, outra variante, produto semelhante, back-in-stock, wishlist, sourcing request e carrinho abandonado consentido.

### Pós-venda

Status, tracking, documento fiscal, troca, devolução e suporte.

### Inteligência comercial

Demanda perdida, perguntas sem resposta, produtos solicitados sem estoque, conversão de alternativas, sourcing conversion e motivo de abandono.

## 18. Regras que evitam um agente ruim

- não inventar produto;
- não cruzar tenant;
- não sugerir concorrente;
- não confirmar pagamento por screenshot;
- não criar urgência falsa;
- não prometer reposição sem sourcing/PO;
- não negociar desconto fora da policy;
- não esconder handoff;
- não guardar PII/conversas indefinidamente;
- não usar LLM como authorization engine.

## 19. Build vs Buy

### Opções a comparar

- Meta Business Agent;
- VTEX/Weni, quando o ecossistema/cliente justificar;
- Blip ou outros provedores de experiência conversacional;
- orchestration própria do Mora Core;
- abordagem híbrida.

### Critérios

- integração com nossos domínios;
- multi-tenancy;
- isolamento de dados;
- lock-in;
- custos;
- observabilidade;
- handoff;
- provider portability;
- Webhooks/API/actions;
- qualidade PT-BR;
- App Review/onboarding;
- suporte;
- SLA;
- compliance e privacidade.

### Recomendação arquitetural

Construir **domínio, tool layer, policies e contracts próprios** independentemente do runtime de IA escolhido.

## 20. Fontes Meta

- Catálogos: https://about.fb.com/br/news/2019/11/apresentamos-o-catalogo-para-pequenas-empresas/
- Compras/pagamentos/atendimento: https://about.fb.com/br/news/2020/10/compras-pagamentos-e-atendimento-ao-cliente-tudo-no-whatsapp/
- Pagamentos no Brasil: https://about.fb.com/br/news/2023/04/agora-voce-pode-pagar-uma-pequena-empresa-no-brasil-pelo-whatsapp/
- WhatsApp Flows: https://about.fb.com/br/news/2023/09/whatsapp-lanca-flows-recurso-que-permite-as-empresas-criarem-experiencias-nativas-no-aplicativo/
- IA/Pix para empresas: https://about.fb.com/br/news/2024/06/novas-ferramentas-de-ia-meta-verified-e-muito-mais-para-empresas-no-whatsapp/
- MPMEs América Latina: https://about.fb.com/br/news/2025/08/26598-pesquisa-revela-o-impacto-da-meta-nas-mpmes-da-america-latina/
- Meta Business Agent: https://about.fb.com/br/news/2026/06/conversations-2026-apresentamos-o-meta-business-agent/
- WhatsApp Business Platform: https://www.postman.com/meta/whatsapp-business-platform/overview/
- Embedded Signup: https://www.postman.com/meta/whatsapp-business-platform/documentation/du6gzjv/embedded-signup

## Relacionados

- [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md)
- [Demanda e Sourcing](../commerce/customer-demand-and-sourcing.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Roadmap conversacional](../roadmap/conversational-commerce-roadmap.md)
- [Benchmark ERP/PDV](competitive-benchmark.md)
