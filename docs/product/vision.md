# Visão do Produto

> **Status:** visão estratégica aprovada como direção; implementação incremental.

## Missão

O Mora Core pretende reduzir o trabalho fragmentado do varejo ao manter produto, estoque, venda, equipe, caixa e canais digitais conectados por uma fonte canônica de dados.

## Problema

Pequenos e médios varejistas frequentemente acumulam:

- ERP/PDV para operação;
- planilhas paralelas;
- fotos no celular;
- site alimentado manualmente;
- marketplaces com cadastros próprios;
- controles de comissão separados;
- relatórios em várias ferramentas;
- divergências entre estoque físico e digital.

O custo não é somente financeiro. Há duplicação de cadastro, erro humano, dificuldade para saber a origem de um saldo, atraso para colocar mercadoria à venda e dependência de conhecimento informal de poucas pessoas.

## Visão

O Mora Core deve ser um **sistema operacional para o varejo físico e digital**.

```text
Mercadoria chega
→ identifica/cadastra
→ cria/valida variantes
→ fotografa
→ IA auxilia conteúdo
→ recebe no estoque
→ imprime etiqueta
→ publica nos canais
→ vende
→ baixa/reserva estoque
→ atualiza caixa/comissão
→ gera analytics com dados reais
```

A frase-chave é:

> **Cadastrar uma vez, operar em vários canais, manter uma única verdade.**

## Público inicial

A primeira operação de referência é varejo de moda e categorias adjacentes, onde existem características como:

- cor;
- tamanho/numeração;
- grade;
- coleção;
- fotos por variante;
- giro e aging;
- múltiplas lojas;
- vendedores e comissão.

A arquitetura deve permitir outros tipos de comércio sem enfraquecer a profundidade necessária para moda.

## Design partner

A Mora funciona como design partner inicial:

```text
problema real
→ observar processo
→ documentar regra
→ implementar capacidade
→ usar na operação
→ medir resultado
→ corrigir
```

O projeto não deve criar dezenas de funcionalidades SaaS baseadas apenas em hipóteses antes de validar a operação real.

## Proposta de valor

### Para o proprietário

- visão de vendas e estoque;
- menos retrabalho;
- rastreabilidade de caixa/comissão;
- visão consolidada de lojas/canais;
- alertas acionáveis;
- possibilidade de expandir venda digital sem cadastros duplicados.

### Para o gerente

- catálogo e estoque consistentes;
- aprovações;
- inventário;
- publicação;
- divergências e tarefas pendentes.

### Para vendedor/caixa

- fluxo de venda rápido;
- consulta de produto/estoque;
- comissão clara conforme política;
- menos menus e privilégios desnecessários.

### Para operador de estoque

- scanner;
- entrada pelo celular;
- variantes;
- fotos;
- etiquetas;
- inventário e transferência.

### Para um futuro cliente SaaS

- começar por importação ou cadastro;
- conectar equipe e lojas;
- criar site se precisar;
- conectar canais;
- manter portabilidade dos próprios dados.

## Diferenciais pretendidos

Nenhum destes itens isoladamente é único: PDV, estoque, mobile, site, marketplace e IA já existem em vários produtos.

A hipótese de diferenciação é a integração do fluxo:

```text
scan → foto → IA assistida → grade → estoque → etiqueta
→ site/canais → venda → caixa/comissão → analytics
```

A hipótese só será considerada validada quando métricas demonstrarem redução de esforço ou melhora operacional.

## Princípios de produto

1. **Regra de negócio antes da tela.**
2. **Produto/variante são canônicos; canais recebem projeções.**
3. **Mobile explora câmera e mobilidade, não replica desktop.**
4. **IA sugere; fatos críticos permanecem determinísticos.**
5. **Dados demonstrativos nunca fingem ser comerciais reais.**
6. **Vendas reais alimentam mais vendidos; estoque não é proxy.**
7. **Segurança e tenant isolation fazem parte do produto.**
8. **Operação deve continuar simples apesar da complexidade interna.**
9. **Exportabilidade reduz lock-in agressivo.**
10. **SaaS-ready não significa overengineering.**
11. **Integrações são tratadas como contratos mutáveis e falíveis.**
12. **Funcionalidade só é considerada existente quando houver evidência.**

## Product Truth e conteúdo gerado

O Mora Core separa:

- **Product Truth:** SKU, variante, cor, material, preço, estoque, dimensões e fatos verificados;
- **Generated Content:** texto comercial, fundo tratado, modelo virtual, social creative e copy por canal.

A IA não pode silenciosamente converter inferência em fato.

## Omnichannel

Um produto pode ser publicado em diversos canais, mas o estoque físico é compartilhado conforme política. O OMS/PIM deverá lidar com listings, pedidos, reservas, fulfillment, devoluções e reconciliação.

## Sites

O storefront é parte da visão porque muitos pequenos lojistas não possuem integração digital consistente. O mesmo catálogo central deve alimentar site próprio e, futuramente, permitir criação de site para novos tenants.

## SaaS

Desde o início, o modelo não assume uma única empresa:

```text
Organization
├── LegalEntity
├── Brand
├── Store
├── StockLocation
├── Employees
├── Websites
└── SalesChannels
```

Multi-tenancy significa isolamento forte e configurabilidade, não duplicação do sistema para cada cliente.

## O que não faremos cedo

- microserviços por módulo;
- Kubernetes por estética;
- fiscal brasileiro completo do zero;
- adquirência própria;
- armazenamento de cartão sem necessidade;
- dezenas de marketplaces;
- previsão por ML sem dados;
- IA autônoma alterando preço/estoque/fiscal;
- editor de site com JavaScript arbitrário;
- multi-região active-active sem requisito real.

## Métricas de sucesso futuras

Além de receita SaaS, medir valor do produto por:

- tempo de cadastro/recebimento;
- percentual de produtos publicados sem retrabalho;
- divergências de estoque;
- tempo de inventário;
- erros de listing;
- tempo de propagação de estoque;
- diferença de caixa;
- tempo de fechamento de comissão;
- adoção por perfil;
- tickets de suporte;
- retenção de clientes;
- custo de IA/tenant;
- disponibilidade das jornadas críticas.

## Evolução

```text
Discovery
→ Foundation
→ Mobile operacional
→ Sites integrados
→ Sales/POS
→ Analytics
→ IA
→ Omnichannel
→ SaaS ampliado
```

A ordem pode mudar com evidência. O roadmap não é promessa rígida de datas.

## Leia também

- [Visão Executiva](executive-overview.md)
- [Módulos](modules.md)
- [Discovery operacional](../discovery/operational-discovery.md)
- [Roadmap](../roadmap/roadmap.md)
- [Benchmark](../research/competitive-benchmark.md)
- [Engineering Constitution](../engineering/constitution.md)
