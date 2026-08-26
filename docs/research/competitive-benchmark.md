# Benchmark Competitivo — ERP, PDV e Varejo

> **Status:** pesquisa estratégica atualizada em 26/08/2026. Funcionalidades, preços, APIs, limites e políticas comerciais mudam; revalidar fontes oficiais antes de decisão de compra, integração ou claim comercial.

## Objetivo

Este documento não existe para escolher “o melhor ERP”. Ele serve para responder:

1. quais problemas sistemas maduros de varejo já resolvem;
2. quais fluxos usuários já esperam de um PDV/ERP;
3. quais diferenciais são específicos de moda e operação multiloja;
4. quais riscos aparecem em offline, fiscal, integrações e suporte;
5. onde o Mora Core pode ser realmente diferente em vez de copiar telas;
6. quais capacidades devem ser validadas na operação real antes de construir.

O benchmark deve alimentar o [roadmap](../roadmap/roadmap.md), [modelo de domínio](../domain/domain-model.md), [mobile](../mobile/mobile-app.md), [omnichannel](../commerce/omnichannel.md) e [arquitetura de integrações](../integrations/reliability-patterns.md).

---

## Resumo executivo

| Produto | Perfil principal | PDV | Offline/contingência | Estoque | Moda/grade | Multi-loja | Mobile | API/ecossistema | Omnichannel | Principal aprendizado para Mora Core |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Bling** | PME/e-commerce/ERP cloud | Sim | operação majoritariamente cloud; validar contingência específica | Forte | variações | Sim/depósitos | web/mobile conforme produto | REST/OAuth2 + webhooks | Forte em integrações | excelente referência de API/webhooks e transição por integração antes de substituição |
| **NOOVA PDV** | varejo físico/local | Sim | Sim, discurso híbrido/contingência | Sim | não confirmado publicamente em profundidade | validar | Windows/Android/Smart POS | API pública não confirmada | foco PDV | velocidade de balcão, TEF/Pix, fechamento e implantação/suporte local |
| **Omie.PDV** | PME com ERP + fiscal | Sim | Sim | Integrado ao ERP | não é foco exclusivo de moda | Sim conforme ERP | Android + desktop | ecossistema/API Omie | integração ERP/PDV | contingência, fiscal e operação com múltiplos usuários |
| **Olist/Tiny** | e-commerce + marketplaces + ERP | Sim | validar por módulo | Forte | variações | recursos conforme plano | web | API V2/V3 e integrações | Muito forte | PIM/listings/pedidos/marketplaces e migração de catálogo |
| **Linx Microvix** | varejo/moda profissional | Sim | Sim/contingência | Muito forte | **Forte**: grade tamanho/cor | Forte | módulos/soluções móveis | ecossistema Linx | Forte | principal benchmark de domínio de moda, grade, etiquetas, giro e omnichannel |
| **TOTVS Varejo/Moda** | redes, moda e enterprise | Sim | soluções variam por linha | Muito forte | **Muito forte** | Muito forte | checkout/venda assistida mobile | grande ecossistema | Muito forte | breadth de varejo, venda assistida, reservas, indicadores e escala; evitar copiar a complexidade |
| **MarketUP** | pequeno varejo/custo baixo | Sim | **Sim**, PDV offline | Sim | genérico | recurso em planos | desktop/web | integrações variam | limitado/moderado | continuidade offline e simplicidade para pequeno comércio |
| **Nex** | pequeno comércio/PDV simples | Sim | **Sim**, desktop/mobile | Sim | genérico | transferências/recursos conforme modo/plano | Sim | ecossistema mais simples | menor foco | UX simples, estoque mobile e operação offline sem enterprise cosplay |

> Células “validar” significam **não assumir** capacidade sem documentação atual suficiente. A matriz é instrumento de produto, não claim publicitário.

---

# 1. Bling

## Posicionamento observado

ERP cloud para PME com compras, vendas, estoque, documentos fiscais, relatórios e forte ecossistema de integrações com e-commerce/marketplaces/logística.

A API oficial atual usa REST + OAuth 2.0. Webhooks oficiais cobrem, entre outros, pedidos, produtos, estoque, estoque virtual, produtos de fornecedor e notas. O contrato de webhook exige resposta rápida e prevê retentativas.

## Capacidades relevantes para o Mora Core

- produto e variações;
- depósitos/estoques;
- compras e fornecedores;
- venda/ordens;
- emissão fiscal;
- integração por API;
- webhooks;
- integração marketplace/e-commerce;
- relatórios.

## O que devemos aprender

### API como produto

Uma integração útil não é apenas “tem endpoint”. Precisa de:

- autenticação documentada;
- scopes;
- limites;
- paginação;
- webhooks;
- versões/payloads;
- erros acionáveis;
- reconciliação quando eventos falham.

O Mora Core deve aplicar isso tanto para integrações externas quanto para sua própria futura API pública.

### Webhook não é transação

Eventos podem atrasar, duplicar ou chegar fora de ordem. Por isso nossa arquitetura prevê:

```text
Webhook
→ validar assinatura
→ dedupe/inbox
→ responder rápido
→ worker
→ buscar estado canônico quando necessário
→ reconciliation periódica
```

### Não substituir antes de provar

A estratégia inicial continua sendo **integrar o Mora Core ao Bling antes de tentar aposentar um ERP que já executa funções críticas/fiscais**.

## Limitações/oportunidades percebidas

- operação depende bastante do modelo cloud;
- experiência mobile de entrada de mercadoria + fotografia + IA não é o diferencial central observado;
- Mora Core pode focar numa UX mais integrada entre mercadoria física, fotos, site próprio e canais.

## Fonte oficial de referência

- https://developer.bling.com.br/bling-api
- https://developer.bling.com.br/webhooks

---

# 2. NOOVA PDV

## Posicionamento observado

Solução local/regional de varejo com foco explícito em balcão rápido, fechamento de caixa, Windows/Android/Smart POS, TEF/Pix e operação híbrida online + contingência.

## Capacidades públicas observadas

- PDV;
- fechamento de caixa;
- pagamentos integrados;
- TEF/Pix;
- Windows;
- Android;
- Smart POS;
- operação online/offline/contingência;
- equipamentos e infraestrutura;
- implantação/suporte.

## Por que é benchmark importante

A NOOVA lembra que “software de varejo” não termina no código. Operação real envolve:

- leitor;
- impressora;
- maquininha/TEF;
- rede/Wi-Fi;
- treinamento;
- contingência;
- suporte quando o caixa para.

Isso precisa aparecer no Discovery do Mora Core antes do nosso PDV real.

## O que Mora Core pode aprender

- reduzir passos no balcão;
- permitir treinamento rápido;
- fechamento com divergência clara;
- integração de pagamento para reduzir digitação/manual reconciliation;
- considerar hardware desde o desenho do PDV;
- suporte operacional como parte do produto SaaS.

## O que ainda NÃO foi confirmado publicamente

Antes de qualquer integração/decisão, perguntar diretamente:

- existe API pública?
- webhooks?
- OAuth/token?
- sandbox?
- limites?
- exportação completa?
- multi-CNPJ/multi-loja?
- banco/local cache e estratégia de sync?
- resolução de conflito offline?
- política de backup?
- integração com site próprio?
- extensibilidade/ISV?

Não inferir white-label ou API sem prova.

## Fonte

- https://www.noovatecnologia.com.br/

---

# 3. Omie / Omie.PDV Varejo

## Posicionamento observado

ERP de PME com módulo de PDV integrado e operação fiscal. A documentação atual do Omie.PDV informa operação offline/contingência: vendas continuam sem internet e sincronizam com ERP/SEFAZ quando a conexão retorna. Também há variante mobile Android.

## Aprendizados

### Offline de PDV é diferente de “app inteiro offline”

O Mora Core não deve prometer offline total. O que precisa sobreviver à falta de internet depende do risco:

- PDV/caixa: continuidade pode ser crítica;
- inventário: ótimo candidato a offline;
- dashboard: pode esperar;
- autorização privilegiada/fiscal: pode exigir restrições adicionais.

### Fiscal + contingência

Venda offline não significa ignorar obrigação fiscal. Estados, séries, contingência e posterior transmissão exigem desenho específico. O módulo fiscal do Mora Core deve ficar atrás de adapter e só avançar com validação contábil/fiscal.

## O que comparar em piloto futuro

- tempo para abrir/finalizar venda;
- tratamento de queda de internet;
- reconciliação após reconectar;
- caixa/turno;
- TEF/Pix;
- emissão fiscal;
- gestão de usuários;
- facilidade de suporte.

## Fonte

- https://ajuda.omie.com.br/pt-BR/articles/9507240-perguntas-frequentes-omie-pdv-varejo

---

# 4. Olist ERP / Tiny

## Posicionamento observado

Muito relevante para quem vende em marketplaces/e-commerce. A página atual da Olist Tiny destaca gestão de marketplaces, integração com dezenas de plataformas de e-commerce e PDV. A API possui fluxos para contatos, produtos, estoque, preços, pedidos, PDV, notas e expedição. A documentação sinaliza evolução de API V2 para V3.

## Aprendizados

### Omnichannel é problema de sincronização, não só publicação

Precisamos tratar:

- mappings de produto/variante;
- preço por canal;
- estoque/reservas;
- pedido externo;
- status;
- nota;
- expedição;
- etiqueta/tracking;
- devolução;
- drift/reconciliation.

### APIs evoluem

O fato de coexistirem versões mostra por que `MarketplaceAdapter`/`ERPAdapter` é importante. Contrato externo não deve vazar para o domínio canônico.

### Integração como ferramenta de migração

Olist/Tiny é também benchmark para o problema futuro:

> “Quero usar Mora Core, mas já tenho milhares de produtos em outro sistema.”

Mora Core precisa de importação versionada, dry-run, mapping e reconciliação.

## Fontes

- https://tiny.com.br/
- https://tiny.com.br/api-docs/api

---

# 5. Linx Microvix

## Posicionamento observado

ERP especializado em varejo, particularmente valioso como benchmark de moda. A documentação/site público mostra grade por tamanho e cor, imagens, custo/preço, código de barras por item de grade, estoque, consignação, catálogo digital, giro e vendas offline/contingência.

## Por que é um dos benchmarks mais importantes

Moda não pode ser modelada como:

```text
Product
sizes[]
colors[]
quantity
```

O benchmark reforça a necessidade de **grade/variant matrix**:

```text
Produto
  ├── Azul / P
  ├── Azul / M
  ├── Preto / P
  └── Preto / M
```

Cada variante pode ter:

- SKU;
- barcode;
- custo;
- preço;
- saldo;
- mídia;
- status.

## O que Mora Core deve incorporar conceitualmente

- cadastro rápido tamanho × cor;
- barcode por variante;
- etiqueta;
- coleção/linha quando relevante;
- giro/aging;
- planejamento de compras baseado em dados;
- estoque entre lojas;
- troca/entrada/saída rastreável;
- indicadores de moda.

## Oportunidade de diferenciação

Mora Core pode tentar entregar essa riqueza de domínio com UX mais moderna/mobile-first e integração nativa com:

```text
foto → IA → variante → estoque → site → marketplace
```

sem reproduzir a complexidade histórica de um ERP enterprise.

## Fontes

- https://www.linx.com.br/erp-microvix/
- documentação Linx Microvix de grade/cadastro de produtos.

---

# 6. TOTVS Varejo / TOTVS Moda / PDV Omni

## Posicionamento observado

Benchmark de grande abrangência para varejo e moda. A oferta atual descreve gestão da retaguarda ao PDV, estoque, grade, reposição, indicadores e omnichannel. O PDV Omni possui checkout, checkout mobile, self-checkout e venda assistida/mobile.

## Capacidades particularmente relevantes

- checkout tradicional/mobile;
- venda assistida;
- reserva de estoque no pedido;
- consulta de estoque na loja/rede;
- identificação de vendedor;
- descontos/promos;
- pedidos e múltiplos tipos de retirada;
- indicadores por loja;
- comissões e ticket médio no ecossistema moda;
- grade/estoque;
- integração físico + digital.

## Aprendizados para Mora Core

### Venda assistida ≠ caixa

O vendedor pode construir o pedido antes do cashier receber. Isso confirma nossa separação `sellerId != cashierId`.

### Reserva de estoque

Pedido não finalizado pode exigir reserva para evitar overselling. Essa capacidade conecta POS, OMS e inventory.

### Rede de lojas

Consulta e venda assistida precisam conhecer estoque local e, futuramente, de outras unidades conforme permissão.

### Mobile de gestão

Executivos/gerentes esperam acompanhar indicadores sem depender do desktop — reforça Mora Mobile como ferramenta também gerencial.

## O que NÃO copiar

TOTVS atende cenários enormes. Mora Core não deve replicar por antecipação:

- dezenas de módulos enterprise;
- complexidade de implantação;
- microserviços/distributed systems sem necessidade;
- workflows voltados a redes enormes antes de validar pequenos varejistas.

## Fontes

- https://www.totvs.com/varejo/
- https://www.totvs.com/varejo/pdv-omni/
- https://produtos.totvs.com/ficha-tecnica/totvs-varejo-pdv-omni/
- https://produtos.totvs.com/ficha-tecnica/tudo-sobre-o-totvs-moda/

---

# 7. MarketUP

## Posicionamento observado

ERP/PDV de baixo custo voltado ao varejo. A central oficial informa PDV online e offline. O modo offline pode continuar vendendo por período limitado e exige posterior sincronização/transmissão fiscal.

## Aprendizados

- pequeno comércio valoriza continuidade mais do que arquitetura “bonita”;
- offline precisa de política temporal e reconciliação;
- cada PDV pode ter requisitos fiscais/série próprios;
- dashboard online pode ser eventualmente consistente enquanto o caixa continua localmente.

## Mora Core

Offline inicial deve ser seletivo:

- inventário/rascunho mobile primeiro;
- PDV offline somente depois de threat model, fiscal e sync definidos;
- `operationId`/idempotência;
- conflito explícito;
- reconciliation.

## Fontes

- https://suporte.marketup.com/hc/pt-br/articles/202943459-PDV-Como-funciona-o-PDV-Offline
- https://suporte.marketup.com/hc/pt-br/articles/33735591352852-PDV-Tudo-sobre-o-PDV-Ponto-de-Venda-no-MarketUP

---

# 8. Nex

## Posicionamento observado

PDV/estoque de abordagem mais simples para pequenos comércios. A documentação atual descreve vendas online/offline, sincronização após reconexão e capacidades de estoque em desktop, web e aplicativo.

## Capacidades úteis como referência

- venda simples;
- operação offline;
- consulta de estoque no app;
- movimentações;
- transferência entre lojas;
- informações financeiras mobile;
- UX voltada a pequeno negócio.

## Aprendizado

Nem todo usuário quer um ERP com cem menus. O Mora Core deve aplicar progressive disclosure:

```text
vendedor → tarefas de venda
estoque → recebimento/inventário
manager → controle/aprovação
owner → configuração/analytics
```

A complexidade do domínio não precisa virar complexidade de tela.

## Fonte

- https://help.nextar.com/tutorial/learn-about-nexs-platform-mode

---

# 9. Matriz de capacidades que o Mora Core precisa validar

Legenda: `C` = capacidade essencial; `E` = evolução; `?` = depende de discovery.

| Capacidade Mora Core | Prioridade | Benchmark que reforça |
| --- | --- | --- |
| produto + variante/SKU | C | Linx, TOTVS, Bling, Olist |
| grade tamanho × cor | C para moda | Linx, TOTVS |
| barcode/etiqueta por variante | C | Linx, TOTVS, Nex |
| ledger de estoque | C | necessidade de integridade transversal |
| múltiplos stock locations | C | Bling, Linx, TOTVS |
| transferência entre lojas | C/E conforme piloto | Linx, Nex, TOTVS |
| PDV | C posterior à foundation | todos |
| caixa/turno/fechamento | C para PDV | NOOVA, MarketUP, TOTVS |
| seller separado de cashier | C | TOTVS venda assistida + operação Mora |
| comissão auditável | C quando PDV entrar | TOTVS Moda + requisito Mora |
| offline inventário | E cedo | Nex, apps móveis |
| offline PDV | E de alto risco | NOOVA, Omie, MarketUP, Linx |
| TEF/Pix integrado | E | NOOVA, Omie |
| fiscal adapter | C antes de substituir ERP | Bling, Omie, MarketUP, TOTVS |
| API + webhooks | C arquitetural | Bling, Olist |
| marketplace hub | E | Olist, Bling |
| OMS/reservas | E | TOTVS, Olist |
| mobile operacional | C diferencial | Nex, TOTVS, NOOVA |
| foto + IA + publicação | C diferencial após foundation | oportunidade Mora Core |
| site builder integrado | E/diferencial | landscape e-commerce |
| multi-tenant SaaS | C arquitetural | objetivo estratégico Mora Core |

---

# 10. O que já existe no mercado e não é diferencial isoladamente

Sozinhos, estes itens não tornam Mora Core único:

- PDV;
- estoque;
- caixa;
- dashboard;
- código de barras;
- multi-loja;
- offline;
- marketplace;
- API;
- site;
- app mobile.

São capacidades esperadas em diferentes níveis.

## Diferenciação pretendida

A hipótese diferenciadora é a **integração do fluxo inteiro**, especialmente para pequenos/médios varejistas:

```text
Receber mercadoria no celular
→ scan ou gerar código
→ cadastrar variantes
→ fotografar produto real
→ IA melhora/estrutura conteúdo
→ gerar etiqueta
→ estoque entra
→ publicar em site/canais
→ vender físico/digital
→ estoque sincroniza
→ caixa/comissão/analytics atualizam
```

O diferencial só será real se conseguirmos medir:

- redução de tempo de cadastro;
- menor retrabalho;
- menos divergência de estoque;
- menos erro de listing;
- rapidez da entrada até publicação;
- confiabilidade do caixa;
- clareza de comissão;
- facilidade de onboarding.

---

# 11. O que copiar, adaptar e evitar

## Copiar como princípio

- Bling/Olist: integrações como capacidade de primeira classe;
- Linx/TOTVS: domínio real de moda/grade/multiloja;
- NOOVA/Omie/MarketUP/Nex: levar offline/contingência a sério;
- TOTVS: separar venda assistida e caixa;
- Nex: simplicidade para pequenos comércios;
- todos: suporte/implantação são parte da experiência.

## Adaptar

- enterprise workflows → progressive disclosure;
- ERP-centric UI → mobile-first para entrada e inventário;
- marketplace hub → poucos adapters excelentes no começo;
- relatórios extensos → métricas acionáveis por papel.

## Evitar

- copiar arquitetura interna de concorrente sem contexto;
- feature parity como roadmap;
- construir fiscal do zero cedo;
- suportar 30 marketplaces antes de 3 funcionarem bem;
- offline total sem modelo de conflito;
- dashboards com métricas sem fonte/auditabilidade;
- configuração tão flexível que ninguém consegue implantar.

---

# 12. Gaps/oportunidades que a hipótese Mora Core explora

## 12.1 Entrada de mercadoria mobile-first

Scanner + fotografia + grade + recebimento na mesma jornada.

## 12.2 Product Truth + IA

IA gera descrição, classificação, fundo, modelo virtual e conteúdo por canal **sem virar autoridade sobre fatos do produto**.

## 12.3 Site como extensão natural do ERP

Lojista que não possui site consegue criar storefront alimentado pelo mesmo catálogo/estoque.

## 12.4 Comissão transparente no app

Funcionária vê suas próprias vendas e comissão provisionada sem ter acesso indevido a margem/custo/dados de colegas.

## 12.5 SaaS sem abandonar operação física

Muitos produtos se especializam em ERP físico ou e-commerce. Mora Core pretende tratar ambos como partes do mesmo domínio, mantendo segurança multi-tenant.

---

# 13. Discovery obrigatório antes de codificar o PDV

Comparar a operação real da Mora com pelo menos:

- cadastro/grade;
- entrada por XML/manual;
- etiqueta/barcode;
- ajuste/inventário;
- transferência;
- abertura de caixa;
- venda;
- desconto/autorização;
- vendedor;
- pagamento/TEF/Pix;
- NFC-e/NF-e;
- cancelamento;
- devolução/troca;
- sangria/suprimento;
- fechamento;
- comissão;
- relatórios;
- contingência sem internet;
- exportação/contador;
- suporte/hardware.

O resultado deve virar requisitos/ADRs, não apenas screenshots de concorrente.

---

# 14. Como manter este benchmark vivo

Para cada revisão relevante:

1. registrar data;
2. usar fonte oficial sempre que possível;
3. separar fato público de inferência;
4. não congelar preço sem necessidade;
5. atualizar apenas claims que tenham evidência;
6. transformar aprendizado relevante em requirement/ADR/issue;
7. não perseguir feature só porque concorrente possui.

## Documentos relacionados

- [Landscape de mercado](market-landscape.md)
- [Roadmap](../roadmap/roadmap.md)
- [Rastreabilidade de requisitos](../roadmap/requirements-traceability.md)
- [Aplicativo mobile](../mobile/mobile-app.md)
- [Vendas, caixa e comissões](../domain/sales-cash-commissions.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Marketplaces](../integrations/marketplaces.md)
- [Arquitetura](../architecture/system-architecture.md)
