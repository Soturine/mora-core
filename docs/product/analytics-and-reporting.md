# Analytics e Relatórios

> **Status:** métricas e princípios planejados. Definições finais dependem de dados e regras reais.

## Princípio

Relatório útil precisa responder uma pergunta de negócio e permitir rastrear o número até os fatos que o originaram.

Evitar dashboards com números bonitos sem definição, contexto ou ação possível.

## Fontes

Analytics comercial deriva principalmente de:

- `Sale` / `SaleItem`;
- `Payment`;
- `Return`/`Exchange`;
- `InventoryMovement`;
- `InventoryBalance`;
- `CashSession`/`CashMovement`;
- `CommissionEntry`/`Statement`;
- `Order`/`ChannelListing`;
- custo/fornecedor quando confiáveis.

Observabilidade técnica é outra disciplina.

---

# Métricas de vendas

## Faturamento

Dimensões possíveis:

- dia/semana/mês/período;
- loja;
- canal;
- categoria;
- produto/variante;
- vendedor quando aplicável.

Definição precisa dizer como trata:

- cancelamento;
- devolução;
- descontos;
- frete;
- impostos/taxas quando incluídos;
- timezone da loja.

## Unidades vendidas

Soma de quantidades de `SaleItem` elegíveis, líquida de reversões conforme regra.

## Ticket médio

Definição padrão possível:

```text
vendas líquidas elegíveis / número de vendas elegíveis
```

Mas precisa de política explícita para devoluções e pedidos parcialmente cancelados.

## Mais vendidos

Nunca usar estoque atual como proxy.

```text
SaleItem elegível
→ janela (30/90 dias/temporada)
→ reversões aplicadas
→ agregação por produto/variante
→ ranking
```

Pode existir por loja, categoria e canal.

## Vendas por horário/dia

Útil para escala e operação, desde que timezone seja correto.

---

# Métricas de vendedor

- vendas próprias;
- unidades;
- ticket médio;
- comissão provisionada;
- comissão aprovada/fechada;
- metas quando configuradas;
- devoluções atribuídas conforme política.

Marketplace/site normalmente não possui `sellerId` humano. Não atribuir venda online aleatoriamente.

### Privacidade

O fato de um número existir não significa que todo funcionário pode vê-lo. Policies definem visibilidade.

---

# Estoque

## Estoque baixo/zerado

Threshold pode ser configurado por variante/local/categoria ou derivado de policy futura. Evitar um único limite universal sem contexto.

## Giro

Relaciona venda e estoque médio em período. Fórmula precisa ser documentada antes do relatório virar oficial.

## Sell-through

Exemplo conceitual:

```text
unidades vendidas / unidades recebidas ou disponíveis no período
```

A definição escolhida precisa ser consistente.

## Aging / produto parado

Tempo desde recebimento/publicação/última venda, dependendo da pergunta.

## Curva ABC

Pode classificar por receita, unidades, margem ou outra base. O relatório deve mostrar qual dimensão usa.

## Divergência de inventário

- esperado;
- contado;
- ajuste;
- razão;
- local;
- frequência.

---

# Margem e rentabilidade

Só publicar margem se custo for confiável.

Para canais digitais, margem real pode exigir:

- preço líquido;
- custo;
- taxa do marketplace;
- comissão de pagamento;
- frete/subsídio;
- devolução;
- imposto quando modelado.

Não chamar “faturamento” de “lucro”.

---

# Caixa

- caixas abertos;
- esperado vs contado;
- divergências;
- sangrias;
- suprimentos;
- vendas por meio de pagamento;
- fechamento pendente;
- frequência de override/aprovação.

---

# Comissão

- provisionada;
- aprovada;
- revertida;
- settled/fechada;
- por período;
- por funcionário;
- composição até venda/item.

Relatório não recalcula história com plano atual; usa snapshots/entries históricos.

---

# Canais e e-commerce

- vendas por canal;
- pedidos;
- cancelamento/devolução;
- taxa de rejeição de listing;
- produtos ativos;
- tempo de publicação;
- stock-sync lag;
- drift detectado;
- margem/taxas quando disponíveis.

## Performance do site

No futuro:

- visualização de produto;
- CTA WhatsApp;
- add-to-cart/checkout se existir;
- conversão;
- origem de tráfego com privacidade adequada.

Analytics de marketing não deve capturar PII desnecessária.

---

# Dashboard por papel

## Proprietário

Priorizar:

```text
Hoje
├── vendas por loja
├── total consolidado
├── ticket médio
├── peças
├── estoque crítico
├── produtos parados
├── caixas/divergências
├── vendas por canal
└── tarefas/alertas
```

## Gerente

- loja(s) sob responsabilidade;
- equipe;
- estoque;
- publicação;
- caixa;
- aprovações.

## Vendedor

Conforme política:

- próprias vendas;
- peças;
- comissão estimada;
- meta;
- últimos resultados.

Não mostrar margem/custo ou dados de colegas sem autorização.

---

# Definições e catálogo de métricas

Cada métrica implementada deve possuir ficha:

```text
nome
pergunta respondida
definição/fórmula
fonte
filtros
timezone
tratamento de cancelamento/devolução
owner
permissões
freshness
limitações
```

Isso evita que web, app e relatório usem fórmulas diferentes.

## Dados em tempo real x eventual

Nem toda métrica precisa ser síncrona:

- saldo operacional crítico: freshness curta;
- dashboard: segundos/minutos podem ser aceitáveis;
- relatório ABC mensal: pode ser batch.

O contrato de freshness precisa ser explícito.

## Arquitetura

Começar com consultas/projeções dentro do PostgreSQL/monólito quando suficiente. Data warehouse, streaming e lake só entram com necessidade mensurável.

## Qualidade de dados

Relatório deve sinalizar quando falta dado essencial, por exemplo margem sem custo confiável.

Não preencher lacuna com zero silenciosamente quando zero e desconhecido têm significado diferente.

## Testes

- fixtures com vendas/cancelamentos/devoluções;
- timezone boundaries;
- valores monetários exatos;
- filtros por tenant/loja;
- comissão histórica;
- ranking com empate;
- performance em volume representativo;
- reconciliação do agregado contra transações.

## Relacionados

- [Vendas, caixa e comissões](../domain/sales-cash-commissions.md)
- [Estoque](../domain/inventory.md)
- [SRE/AIOps](../operations/sre-aiops.md)
- [Personas](personas-and-journeys.md)
