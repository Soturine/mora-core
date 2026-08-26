# Auditoria da Documentação do Repositório — 26/08/2026

> **Escopo:** revisão estrutural e de coerência do repositório `mora-core` como material de apresentação para proprietários/família e como foundation para engenharia futura.
>
> **Resultado geral:** a documentação possui cobertura forte e boa separação por domínio. A auditoria identificou principalmente gaps de **amarração entre documentos**, não grandes funcionalidades ausentes. Esses gaps foram corrigidos com um cenário futuro completo das lojas Mora, fluxos técnicos end-to-end, atualização do modelo de domínio e melhorias de navegação.

## 1. O que foi verificado

- README como entrada do projeto;
- visão executiva;
- módulos e capacidades;
- arquitetura;
- domínio;
- multi-tenancy/SaaS;
- mobile;
- IA/mídia;
- estoque;
- compras/fornecedores;
- vendas/PDV;
- caixa;
- comissão;
- storefront/sites;
- WhatsApp/commerce conversacional;
- inbox/handoff/copiloto;
- busca por foto;
- demanda perdida;
- sourcing/viagens de compra;
- marketplaces;
- fiscal/pagamentos;
- Bling;
- analytics;
- segurança/LGPD;
- QA;
- DevSecOps;
- SRE/AIOps;
- performance/capacidade;
- backup/recovery;
- benchmarks;
- roadmap;
- rastreabilidade;
- regras para agentes futuros.

## 2. Forças encontradas

### Cobertura funcional

Os grandes domínios discutidos estão documentados e possuem arquivos próprios ou seções canônicas.

### Separação de responsabilidades

A documentação diferencia corretamente:

- `User` vs `Employee`;
- `seller` vs `cashier`;
- venda vs pedido;
- pagamento vs caixa;
- comissão vs total mensal mutável;
- estoque físico/reservado/disponível;
- Product Truth vs Generated Content;
- Organization vs Store vs StockLocation;
- billing SaaS vs pagamentos dos clientes das lojas;
- atendimento de conversa vs atribuição de comissão;
- demanda não atendida vs venda real;
- interesse do cliente vs sourcing vs compra confirmada.

### Segurança multi-tenant

A fronteira `Organization` aparece como requisito transversal, inclusive em:

- APIs;
- queries;
- jobs;
- cache;
- embeddings/vector search;
- busca por imagem;
- recomendações;
- WhatsApp;
- marketplaces;
- analytics;
- exports.

### Honestidade de status

Os documentos distinguem visão/planejamento de implementação real e evitam transformar decisão ainda não tomada em fato.

## 3. Gap principal identificado e corrigido

Antes desta auditoria, existiam bons documentos especializados, mas faltava uma narrativa única que respondesse:

> “Como tudo isso funcionará junto nas lojas Mora quando o sistema estiver maduro?”

Foi criado:

- [`docs/product/mora-reference-future-state.md`](../product/mora-reference-future-state.md)

Ele conecta, em um exemplo contínuo:

```text
planejamento de compra
→ viagem/fornecedor
→ recebimento
→ cadastro/variantes
→ fotos/IA
→ estoque
→ publicação
→ site/WhatsApp/marketplaces/PDV
→ reserva
→ pagamento
→ fiscal
→ venda
→ caixa
→ comissão
→ troca/devolução
→ analytics
→ nova compra
```

Também demonstra falhas/contingência, segurança e evolução SaaS.

## 4. Gap técnico identificado e corrigido

Faltava um mapa de sequência entre módulos para evitar que o cenário executivo ficasse desconectado da arquitetura.

Foi criado:

- [`docs/architecture/end-to-end-flows.md`](../architecture/end-to-end-flows.md)

O documento cobre os principais fluxos técnicos e seus boundaries, incluindo idempotência, integração e tenant context.

## 5. Modelo de domínio atualizado

O `domain-model.md` não listava explicitamente algumas entidades surgidas na expansão recente de commerce conversacional e sourcing.

Foram incorporados conceitualmente:

- `CustomerIdentity`;
- `Wishlist`;
- `BackInStockSubscription`;
- `Conversation`;
- `ConversationMessage`;
- `ConversationAssignment`;
- `Cart` / `CartItem`;
- `CustomerRequest`;
- `DemandSignal`;
- `SourcingRequest`;
- `ProcurementTrip`;
- `SourcingCandidate`;
- `PurchaseSuggestion`;
- `FiscalDocument`.

Também foram adicionadas invariantes específicas para cross-tenant, demanda, sourcing e atribuição de atendimento.

## 6. Visão executiva

A visão executiva já era forte e cobre:

- problema de retrabalho;
- mercadoria/estoque;
- mobile;
- IA;
- funcionárias;
- caixa/comissão;
- sites;
- WhatsApp;
- busca por foto;
- demanda não atendida;
- sourcing;
- marketplaces;
- pagamento/fiscal;
- dashboards;
- SaaS;
- segurança.

O cenário futuro detalhado complementa esse documento sem transformá-lo em um manual técnico excessivamente longo.

## 7. Exemplo realista usando a Mora

O cenário de referência usa a Mora como primeira organização/design partner, mas preserva a distinção entre:

- fatos conhecidos sobre a direção do projeto;
- exemplos ilustrativos de produto/valores;
- regras que dependem do Discovery;
- capacidades planejadas ainda não implementadas.

Isso evita inventar CNPJ, percentuais, preços, fornecedores ou políticas reais.

## 8. Coerência do fluxo de estoque

A documentação converge para:

```text
InventoryMovement append-only
→ InventoryBalance derivado/materializado
```

Entradas/saídas passam por eventos/movimentos rastreáveis. Inventário gera contagem e ajuste autorizado, não sobrescrita silenciosa.

Reservas participam da disponibilidade para canais online.

## 9. Coerência de vendas e comissão

Venda finalizada não é editada silenciosamente.

Correções usam:

- cancelamento;
- devolução;
- refund;
- reversão;
- eventos compensatórios.

Comissão é ledger, permitindo explicar o total mensal e reverter efeitos de devolução/cancelamento.

## 10. Coerência de commerce conversacional

A arquitetura separa:

```text
LLM/conversation
→ Tool/Policy Layer
→ domínios determinísticos
```

A IA não é autoridade de:

- preço;
- estoque;
- desconto;
- tenant;
- autorização;
- pagamento;
- fiscal;
- compra de fornecedor.

## 11. Coerência de canais externos

Produto canônico → listing por canal.

Cada canal pode ter conteúdo/preço/regra próprios, mas nenhum adapter define o modelo interno.

Pedidos externos usam inbox/dedupe/reconciliation para lidar com retries e eventos fora de ordem.

## 12. Fiscal

A documentação corretamente evita assumir que “o governo oferece uma API única para qualquer nota”.

A direção de risco é:

```text
início: Bling / solução fiscal existente
→ depois: FiscalPort + provider
→ integração SEFAZ direta somente se benefício justificar
```

## 13. Segurança, QA e operação

A documentação já cobre os pilares importantes:

- threat model;
- AuthN/AuthZ/object auth/tenant isolation;
- RBAC;
- audit;
- secrets;
- upload;
- IA não confiável;
- SAST/SCA/secret scanning;
- supply chain;
- testes por risco;
- concorrência/idempotência;
- recovery testing;
- CI/exact SHA;
- observabilidade;
- SLI/SLO quando houver dados;
- backup/restore;
- runbooks.

## 14. Navegação

O índice foi atualizado para começar por:

1. Visão Executiva;
2. cenário completo das lojas Mora;
3. fluxos técnicos end-to-end;
4. Discovery;
5. roadmap;
6. rastreabilidade.

Isso atende melhor dois públicos:

- proprietário/família;
- engenharia/agentes.

## 15. Itens ainda deliberadamente abertos

Continuam abertos porque precisam de fato real, não porque foram esquecidos:

- topologia de CNPJ;
- política real de comissão;
- política de desconto;
- troca/devolução;
- reserva;
- fornecedores/fluxo real de compra;
- hardware;
- TEF/adquirente/Pix;
- estratégia de entrega;
- provider fiscal;
- backend/framework;
- ORM;
- auth;
- cloud;
- queue/jobs;
- runtime de IA;
- contratos dos marketplaces;
- SLO/RPO/RTO;
- planos/preços SaaS;
- regras jurídicas finais.

Esses itens devem sair de Discovery, ADR, POC ou validação externa.

## 16. Limitações desta auditoria

A inspeção automatizada local de links por `git clone` não pôde ser executada no ambiente auxiliar porque o container não possuía resolução de rede para `github.com`. A árvore, os arquivos centrais e a navegação foram auditados diretamente pelo conector GitHub.

Por isso, uma futura CI de documentação pode incluir um link checker quando o repositório tiver pipeline executável. Isso deve ser um gate útil, não um check ornamental.

## 17. Resultado

### Cobertura de visão

**Forte.**

### Coerência de negócio

**Forte, com decisões reais pendentes explicitadas.**

### Coerência arquitetural

**Forte como architecture foundation; stack concreta ainda depende de ADR.**

### Exemplo compreensível para a família

**Fortalecido nesta auditoria com o cenário futuro completo da Mora.**

### Prontidão para implementação

**Ainda não.** O próximo gate correto continua sendo concluir Discovery e congelar ADRs iniciais, não interpretar a quantidade de documentação como software pronto.

## Próximos passos recomendados

1. revisar o cenário futuro com os proprietários;
2. preencher Discovery operacional;
3. marcar o que é requisito real, hipótese ou não desejado;
4. transformar decisões estruturais em ADRs;
5. definir Milestone 1 e critérios de aceitação;
6. então iniciar código com migrations, tenancy, auth, catálogo e estoque.
