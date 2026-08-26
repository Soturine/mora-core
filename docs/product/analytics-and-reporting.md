# Analytics e Relatórios

## Princípio

Relatório deve apoiar decisão e ser rastreável aos dados transacionais.

## Métricas planejadas

- faturamento por loja/canal/período;
- unidades vendidas;
- ticket médio;
- mais vendidos por janela;
- margem quando custo confiável existir;
- estoque baixo/zerado;
- giro e sell-through;
- aging/encalhados;
- curva ABC;
- devoluções/trocas;
- desempenho por vendedor;
- comissão provisionada/fechada;
- divergência de caixa;
- formas de pagamento;
- performance de marketplace/site.

## Mais vendidos

Baseado em `SaleItem` concluído/elegível, não saldo de estoque. Janela e tratamento de devolução/cancelamento são regra backend.

## Vendedor

Marketplace/site normalmente têm `sellerId=null` salvo atribuição comercial explícita. Não creditar venda online aleatoriamente a funcionário.

## Privacidade

Vendedores veem apenas métricas permitidas; owner/manager possuem visão maior. Custos/margens podem ser restritos.

## Analytics operacional != telemetria técnica

Business analytics e observabilidade técnica são domínios distintos, embora possam compartilhar infraestrutura de dados no futuro.
