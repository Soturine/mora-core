# Escopo, Prioridades e Não Objetivos

> **Status:** guardrail de produto. Atualizar quando roadmap ou estratégia mudar.

## Por que este documento existe

O Mora Core possui uma visão ampla. Sem limites explícitos, a equipe pode tentar construir simultaneamente ERP, PDV, app, fiscal, marketplace hub, IA, site builder e SaaS billing — aumentando risco e atrasando valor real.

Este arquivo separa:

- visão de longo prazo;
- escopo de cada fase;
- capacidades deliberadamente adiadas;
- complexidades que só entram com evidência.

## Escopo de longo prazo

- identidade, organização e permissões;
- catálogo e variantes;
- estoque;
- compras/fornecedores;
- vendas/PDV;
- pagamentos;
- caixa;
- comissão;
- clientes/CRM leve;
- mobile;
- mídia;
- IA assistida;
- sites/storefront;
- PIM/OMS;
- marketplaces;
- analytics;
- integrações;
- fiscal por adapter;
- multi-tenancy;
- billing/entitlements SaaS;
- segurança, observabilidade e operação em nuvem.

## Prioridade inicial

O primeiro objetivo não é “lançar um SaaS completo”. É construir um núcleo confiável para a operação real:

```text
Organization/Store
→ Identity/Permissions
→ Catalog/Variants
→ Inventory ledger
→ Audit
```

Depois as superfícies de mobile/site/venda crescem em cima desse núcleo.

## Fase 0 — Discovery

Inclui:

- processos atuais;
- Bling;
- CNPJs/lojas/estoques;
- catálogo real;
- hardware;
- caixa;
- comissão;
- fiscal;
- pagamentos;
- conectividade;
- sites/canais.

Não inclui escrever código de produção sem regras suficientes.

## Foundation

Deve produzir capacidades reais e testáveis, não telas vazias:

- autenticação/authorization baseline;
- organization/store;
- catálogo/variante;
- migrations;
- inventory movements;
- audit;
- contratos API;
- observabilidade mínima;
- testes e CI correspondentes.

## Fora da Foundation

- checkout próprio completo;
- fiscal próprio;
- marketplace adapters produtivos;
- billing SaaS produtivo;
- IA generativa em produção;
- offline completo;
- previsão de demanda;
- microserviços.

## Fora do MVP operacional inicial

### Complexidade distribuída sem evidência

- dezenas de microserviços;
- Kubernetes;
- Kafka;
- service mesh;
- multi-region active-active;
- CQRS cerimonial;
- Event Sourcing completo.

### Infra financeira/fiscal própria

- adquirência;
- armazenamento de cartão;
- TEF próprio;
- protocolo fiscal brasileiro inteiro do zero.

### IA autônoma

- alterar preço;
- publicar fatos não confirmados;
- dar desconto;
- ajustar estoque;
- decidir imposto;
- aprovar usuário;
- executar remediação destrutiva de infraestrutura.

### Commerce excessivo

- marketplace próprio;
- integração com dezenas de canais na primeira versão;
- motor de promoções enterprise antes da necessidade;
- fulfillment/logística próprios onde um adapter resolve.

### Site builder irrestrito

- JavaScript/CSS arbitrário de cliente no início;
- plugins de terceiros sem sandbox/governança;
- page builder que comprometa upgrade/segurança.

## Não objetivos permanentes sem nova decisão estratégica

- aprisionar dados do cliente como estratégia de retenção;
- esconder custo/limites do uso de IA;
- tratar frontend como autoridade de regra crítica;
- usar estoque como métrica de venda;
- expor secrets a cliente;
- permitir acesso cross-tenant por conveniência de suporte.

## Regra de “feature nominal”

Não considerar implementado apenas porque existe:

- tabela;
- interface TypeScript;
- botão;
- endpoint placeholder;
- mock;
- teste que só verifica status 200;
- diagrama.

Feature precisa cumprir critérios de aceitação e evidência proporcional ao risco.

## Critério para adicionar escopo

Antes de colocar uma grande capacidade no roadmap ativo, responder:

1. qual problema real resolve?
2. qual usuário?
3. qual frequência/impacto?
4. existe alternativa simples?
5. quais riscos cria?
6. qual dependência de domínio?
7. como será testada?
8. como será operada?
9. como saberemos se trouxe valor?

## Dívida deliberada x esquecimento

Quando uma capacidade for adiada, registrar:

- motivo;
- risco aceito;
- condição para revisitar;
- owner/milestone quando aplicável.

## Related

- [Roadmap](../roadmap/roadmap.md)
- [Riscos](../roadmap/risks-and-open-questions.md)
- [Visão](vision.md)
- [Engineering Constitution](../engineering/constitution.md)
