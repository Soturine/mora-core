# Módulos e Capacidades

> **Status:** mapa funcional de longo prazo. Cada módulo será implementado por fases; existência neste documento não significa feature pronta.

## Objetivo

Organizar o Mora Core por capacidades de negócio, evitando uma aplicação onde regras de estoque, venda, IA e marketplace ficam misturadas.

A arquitetura padrão continua sendo **monólito modular**.

---

## 1. Identity & Organizations

Responsável por identidade, vínculo e contexto organizacional.

Capacidades:

- autenticação;
- sessão/revogação;
- MFA quando aplicável;
- `Organization`;
- `LegalEntity`;
- `Brand`;
- `Store`;
- `StockLocation`;
- `User`;
- `Employee`;
- memberships;
- roles/capabilities;
- escopo por loja;
- convites;
- lifecycle de funcionário;
- trilha de auditoria de acesso.

Não confundir login com funcionário. Uma identidade pode participar de mais de uma organização; funcionário desligado mantém histórico.

---

## 2. Catalog

Fonte canônica das informações de produto.

Capacidades:

- produto;
- variante;
- SKU;
- GTIN/barcode externo;
- identificador interno;
- categorias hierárquicas;
- atributos estruturados;
- marca;
- coleção;
- faixa etária/audience quando aplicável;
- dados fiscais necessários;
- preços/listas de preço;
- mídia;
- publicação;
- lifecycle `DRAFT → READY → PUBLISHED → ARCHIVED` ou equivalente;
- status de dado verificado/demonstrativo em protótipos.

Para moda, cada combinação válida de atributos precisa de `ProductVariant` real, não apenas arrays soltos de tamanhos e cores.

---

## 3. Inventory

Autoridade sobre movimentação e disponibilidade.

Capacidades:

- ledger de movimentos;
- saldo materializado;
- recebimento;
- venda;
- devolução;
- perda/avaria;
- ajuste autorizado;
- inventário;
- transferência;
- reserva;
- liberação de reserva;
- disponibilidade física/reservada/disponível;
- safety stock por canal;
- múltiplos locais;
- reconciliação.

Saldo nunca é corrigido silenciosamente apenas sobrescrevendo `quantity`.

---

## 4. Purchasing

Gestão de fornecedores e entrada planejada.

Capacidades futuras:

- fornecedores;
- pedido de compra;
- custo;
- condições;
- itens esperados;
- recebimento parcial/total;
- divergência pedido x recebido;
- vínculo com documento fiscal/XML quando apropriado;
- histórico por fornecedor;
- sugestões baseadas em giro somente quando dados forem confiáveis.

---

## 5. Sales

Venda física/digital e correções comerciais.

Capacidades:

- carrinho/venda;
- `SaleItem`;
- seller attribution;
- cashier attribution;
- descontos;
- aprovação;
- múltiplos pagamentos;
- conclusão idempotente;
- cancelamento;
- devolução;
- troca;
- snapshots históricos;
- integração com estoque, caixa, comissão, fiscal e analytics.

Venda concluída não é editada arbitrariamente.

---

## 6. Cash

Controle de caixa/turno.

Capacidades:

- caixa físico/lógico;
- abertura;
- fundo inicial;
- sessão;
- venda em dinheiro;
- reembolso;
- sangria;
- suprimento;
- ajuste;
- fechamento;
- fechamento cego;
- reconciliação por meio de pagamento;
- divergência;
- autorização de gerente;
- histórico/audit.

---

## 7. Commissions

Comissão auditável por funcionário.

Capacidades:

- planos com vigência;
- regras por base;
- provisionamento;
- reversão;
- aprovação;
- fechamento/settlement;
- extrato mensal;
- drill-down até vendas/itens;
- política de devolução/cancelamento;
- métricas próprias no app conforme permissão.

Nenhum percentual será hardcoded como verdade universal.

---

## 8. Customers / CRM leve

Não é prioridade da foundation, mas o modelo deve permitir:

- identificação opcional de cliente;
- contatos com consentimento/finalidade;
- histórico de compras autorizado;
- preferências;
- futura fidelidade/relacionamento;
- export/deleção conforme política.

Evitar coletar PII sem necessidade.

---

## 9. Mobile

Ferramenta operacional baseada em capacidades do celular.

Capacidades planejadas:

- login/tenant/store context;
- scanner;
- produto;
- grade de variantes;
- câmera;
- uploads resilientes;
- código interno;
- etiqueta;
- recebimento;
- inventário;
- transferências;
- consulta de estoque;
- dashboards;
- comissão própria;
- fechamento de caixa autorizado;
- notificações;
- offline seletivo.

---

## 10. Media

Pipeline seguro de ativos.

Capacidades:

- upload original;
- checksum/metadata;
- object storage;
- variantes por produto/cor;
- resize;
- thumbnails;
- WebP/AVIF;
- remoção de EXIF desnecessário;
- processamento assíncrono;
- lifecycle de mídia;
- CDN;
- signed URLs para privado.

---

## 11. AI

IA como assistente, não autoridade transacional.

Capacidades:

- descrição;
- título;
- tags;
- classificação;
- sugestões de categoria;
- melhoria de imagem;
- remoção de fundo;
- modelo virtual;
- conteúdo por canal;
- evals;
- provenance;
- quota/metering;
- AI Gateway;
- revisão humana.

IA nunca decide estoque, preço autorizado, imposto, tenant ou permissão.

---

## 12. Storefront / Websites

Sites próprios alimentados pelo catálogo central.

Capacidades:

- múltiplos sites por organização/marca;
- temas;
- tokens de identidade;
- homepage por blocos seguros;
- categorias dinâmicas;
- página de produto;
- variantes;
- galeria/zoom;
- Novidades;
- Mais Vendidos;
- Destaques;
- Promoções;
- SEO;
- domínio/subdomínio;
- publicação;
- futura jornada de checkout.

---

## 13. Commerce / PIM / OMS

Camada para comércio multicanal.

### PIM

- produto canônico;
- enrichment;
- listing por canal;
- override de título/descrição/preço;
- mapping de categoria/atributo;
- validação de prontidão.

### OMS

- pedido canônico;
- status;
- pagamento;
- reserva;
- separação;
- fiscal;
- fulfillment;
- tracking;
- cancelamento;
- devolução;
- reconciliação.

---

## 14. Integrations

Adapters para contratos externos.

Primeiros candidatos:

- Bling;
- TikTok Shop;
- Mercado Livre;
- Shopee;
- fiscal;
- pagamento/TEF/Pix;
- storage/CDN;
- email/notificação.

Infra comum:

- OAuth/secrets;
- webhooks;
- inbox/outbox quando justificadas;
- retry/backoff;
- rate-limit awareness;
- dead letter;
- reconciliation;
- observabilidade.

---

## 15. Reporting & Analytics

Capacidades planejadas:

- faturamento;
- ticket médio;
- unidades;
- vendas por loja/canal/vendedor;
- mais vendidos;
- estoque baixo/zerado;
- giro;
- sell-through;
- aging/encalhados;
- curva ABC;
- custo/margem quando confiáveis;
- devoluções;
- comissão;
- meios de pagamento;
- caixa/divergências;
- integração/marketplace health comercial.

Analytics de negócio é diferente de telemetria técnica.

---

## 16. Audit

Responsável por eventos de ações sensíveis:

- ator;
- tenant;
- loja/contexto;
- ação;
- recurso;
- before/after sanitizado;
- motivo;
- aprovação;
- request/correlation ID;
- timestamp.

Audit não é debug log e não guarda secrets.

---

## 17. Fiscal

Integração de alto risco atrás de adapter:

- emissão;
- cancelamento;
- status;
- XML;
- contingência;
- vínculo com venda/pedido;
- requisitos de marketplace.

A implementação fiscal brasileira do zero não é objetivo inicial.

---

## 18. Billing SaaS

Domínio separado do dinheiro das vendas das lojas.

Capacidades:

- assinatura;
- plano;
- entitlement;
- quota;
- metering;
- trial;
- grace period;
- billing provider;
- eventos de cobrança;
- suspensão sem corromper dados operacionais;
- export/offboarding.

---

## 19. Platform Operations

Ferramentas internas futuras para operar o SaaS:

- tenant health;
- suporte;
- incident response;
- feature flags;
- migrations;
- jobs/replay;
- integração health;
- billing health;
- observabilidade;
- audit de ações da plataforma.

Qualquer impersonation/support access futuro exige política e auditoria fortes.

---

## Dependências principais

```text
Identity/Organizations
        ↓
Catalog ───→ Inventory ───→ Sales
   │            │             │
   │            │         Cash/Commission
   │            │             │
 Media/AI       └────→ Commerce/OMS
   │                          │
Storefront                Integrations
       \                    /
        └──── Reporting ────┘
```

As setas são conceituais; contratos reais serão formalizados na implementação.

## Regra de ownership

Cada invariável tem um módulo dono. Exemplo:

- saldo → Inventory;
- comissão → Commissions;
- autorização → Identity/Authorization;
- listing externo → Commerce/Integrations;
- mídia original → Media;
- facts de produto → Catalog.

Interfaces não devem duplicar regra crítica.

## Relacionados

- [Arquitetura](../architecture/system-architecture.md)
- [Modelo de domínio](../domain/domain-model.md)
- [Roadmap](../roadmap/roadmap.md)
