# Migração dos Legados Mora — NOOVA, Lips e Demais Fontes

> **Status:** estratégia de migração baseada no levantamento de campo de 05/09/2026.  
> **Objetivo:** definir como extrair, auditar, reconciliar e migrar dados atuais sem transformar inconsistências históricas em fatos canônicos do Mora Core.

Este documento complementa [Importação, Exportação e Migração](import-export-migration.md) e o [baseline real das lojas](../discovery/mora-pos-hardware-baseline-2026-09-05.md).

---

## 1. Fontes atuais conhecidas

Há pelo menos três classes de fonte que precisam ser mapeadas antes de qualquer cutover:

```text
NOOVA Tecnologia          ← observado na loja feminina/familiar
Lips Control              ← observado na loja masculina
Bling / outras fontes     ← confirmar ownership real por capability
```

Além disso podem existir:

- planilhas;
- arquivos de exportação;
- fotos/pastas;
- cadastros em marketplaces;
- dados no site;
- registros fiscais/contábeis;
- histórico de WhatsApp que não deve ser importado indiscriminadamente.

A primeira tarefa não é “importar tudo”. É descobrir **qual fonte é autoridade de qual informação hoje**.

---

## 2. Regra de ownership durante a transição

Para cada capability, declarar explicitamente:

```text
Catalog       → quem é autoridade hoje?
Inventory     → quem é autoridade hoje?
Sales         → quem é autoridade hoje?
Cash          → quem é autoridade hoje?
Fiscal        → quem é autoridade hoje?
Payments      → quem é autoridade hoje?
Customers     → quem é autoridade hoje?
```

Sem isso, duas fontes podem sobrescrever a mesma verdade.

Nunca ativar writes concorrentes entre NOOVA/Lips/Bling/Core sem estratégia documentada.

---

## 3. Prioridade de extração

Preferência:

```text
API/documented export
→ CSV/Excel/database export suportado pelo fornecedor
→ backup/documentação do fornecedor
→ extração assistida
→ automação de UI somente como último recurso temporário
```

Não escolher scraping de tela se existe export/API mais segura e completa.

Antes de qualquer extração de banco/arquivo interno de fornecedor, verificar contrato, permissão e integridade.

---

## 4. Snapshot imutável do legado

Cada migração relevante começa com snapshot bruto e provenance.

```text
LegacySnapshot
├── sourceSystem
├── sourceVersion?
├── organization/store scope
├── extractedAt
├── extractionMethod
├── checksum
├── row/file counts
├── schema/column manifest
└── immutable raw assets
```

O snapshot bruto não é editado para “ficar bonito”. Correções ocorrem no staging/mapping.

---

## 5. Evidências de qualidade que já exigem staging

Na tela de produtos do NOOVA foram observados **5.968 registros** no snapshot de campo, incluindo:

- itens `Sem Categoria`;
- saldos negativos;
- saldos fracionários muito pequenos em unidade `UN`;
- custo zerado em alguns registros;
- descrições históricas heterogêneas.

Esses sinais são suficientes para rejeitar um modelo de migração `export → INSERT direto`.

### Classes iniciais de anomalia

```text
NEGATIVE_BALANCE
FRACTIONAL_UNIT_BALANCE
MISSING_CATEGORY
ZERO_OR_UNKNOWN_COST
DUPLICATE_OR_AMBIGUOUS_IDENTIFIER
UNKNOWN_VARIANT_STRUCTURE
INVALID_OR_MISSING_GTIN
UNMAPPED_TAX_DATA
STALE_OR_INACTIVE_PRODUCT
```

A lista deve evoluir conforme o dump real.

---

## 6. Staging

Fluxo recomendado:

```text
raw snapshot
→ parse
→ normalized staging
→ anomaly detection
→ mapping
→ dedupe candidates
→ review
→ dry-run
→ approved canonical import
```

Staging preserva:

- valor original;
- coluna/origem;
- transformação aplicada;
- decisão humana quando necessária;
- erro/warning;
- target candidate.

Não perder o valor original após normalização.

---

## 7. Quantidade e estoque

### Não confiar cegamente em saldo legado

Um saldo negativo pode representar:

- venda sem entrada anterior;
- sincronização incompleta;
- ajuste histórico;
- item duplicado;
- política que permitia estoque negativo;
- erro operacional.

Um saldo `0.001` em produto `UN` pode representar:

- precision/rounding do legado;
- unidade configurada incorretamente;
- resíduo de cálculo;
- dado real de produto fracionável.

Logo:

```text
legacy balance
→ staging
→ policy/anomaly
→ reconciliação física/operacional
→ OPENING_IMPORT aprovado
```

Nunca arredondar silenciosamente.

### Cutover

Antes do saldo inicial autoritativo:

- definir horário de corte;
- reconciliar vendas ocorridas durante export/import;
- considerar delta sync;
- contar amostra ou inventário conforme risco;
- documentar divergências aceitas.

---

## 8. Produto, variante e grade

O legado pode representar cor/tamanho de forma diferente do modelo desejado.

Precisamos identificar:

```text
legacy product
legacy code
legacy parent?
legacy variation?
brand/reference
GTIN
size/color embedded in description?
```

Não transformar automaticamente cada linha em `Product` independente e depois descobrir que eram variantes do mesmo modelo.

Também não usar IA para mesclar produtos parecidos sem revisão.

---

## 9. Identificadores

Criar mapping estável:

```text
LegacyEntityMapping
├── sourceSystem
├── sourceStore/account
├── entityType
├── externalId/code
├── moraEntityId
├── mappedAt
└── provenance
```

Sinais de identidade:

- external ID;
- código/SKU;
- GTIN;
- referência de fornecedor;
- composição de atributos confirmada.

**Nome não é chave de identidade.**

---

## 10. Dinheiro

Preço/custo importado precisa distinguir:

- zero real;
- ausente;
- desconhecido;
- preço de venda;
- custo;
- promoção;
- vigência.

Nunca transformar `null/unknown` em `0` automaticamente.

Valores canônicos usam centavos/decimal exato conforme ADR.

---

## 11. Categoria e taxonomia

`Sem Categoria` não deve virar uma categoria definitiva do Core por conveniência.

Estratégia:

```text
legacy category
→ mapping table
→ canonical category
```

Quando não houver informação suficiente:

```text
UNCLASSIFIED / REVIEW_REQUIRED
```

como estado de staging, não necessariamente como categoria pública.

O catálogo público só expõe taxonomia aprovada/publicável.

---

## 12. Dados fiscais

NCM, impostos e outros dados fiscais observados no legado são área de alto risco.

Regras:

- preservar exatamente o valor legado e origem;
- não “corrigir” NCM por IA;
- validar com fonte fiscal/contador/provedor;
- manter version/provenance;
- separar ausência de valor de zero/default.

Nenhuma migração de cadastro fiscal autoriza emissão em produção sem homologação.

---

## 13. Vendas históricas

Precisamos decidir quanto histórico realmente migrar.

Possíveis estratégias:

### A. histórico completo normalizado

Mais útil para analytics, mais caro/arriscado.

### B. período recente detalhado + agregado antigo

Pode equilibrar custo e valor.

### C. legado read-only + Core inicia nova história

Mais simples, mas reduz analytics longitudinal.

A decisão depende de:

- disponibilidade de export;
- qualidade;
- obrigação legal;
- comissão;
- devoluções ainda possíveis;
- relatórios necessários.

Não misturar venda histórica importada com venda operacional nova sem provenance.

---

## 14. Clientes e PII

Migrar somente o necessário e permitido.

Antes:

- finalidade;
- base/obrigação;
- minimização;
- retenção;
- acesso;
- dedupe;
- qualidade de telefone/email;
- opt-in/marketing separado.

Não usar screenshot/foto de tela como fonte de cadastro de cliente.

---

## 15. Fiscal/documentos históricos

XML/documentos fiscais podem ter retenção e fluxo próprios.

Não reduzir documento fiscal a colunas genéricas de `Sale`.

Quando migrar/referenciar:

- legal entity;
- chave/protocolo;
- XML íntegro;
- status;
- source/provider;
- vínculo com venda quando confiável;
- checksum.

Validar obrigação de retenção/export com contador/provedor.

---

## 16. Dry-run

Relatório deve permitir decisão operacional.

Exemplo:

```text
5.968 registros de produto lidos
4.900 candidatos válidos
420 sem categoria
83 com saldo negativo
37 com saldo fracionário inesperado
112 possíveis duplicados
16 códigos inválidos
400 exigem revisão de grade
```

Números acima são **exemplo de formato**, não resultados do dataset real. Só o total de 5.968 foi observado na tela de campo; as demais contagens devem ser calculadas no export real.

---

## 17. Reconciliação

Após import:

- contagem de entidades;
- soma/quantidade por categoria de movimento quando comparável;
- amostra de produtos;
- SKUs/GTINs;
- variantes;
- saldos;
- vendas históricas quando migradas;
- documentos fiscais;
- clientes conforme escopo.

Qualquer diferença relevante vira relatório, não ajuste invisível.

---

## 18. Shadow/dual-run

Antes de substituir o PDV atual:

```text
legado continua produção
→ Core recebe/simula projeção
→ comparar resultados
→ corrigir gaps
→ piloto controlado
→ fallback
→ cutover somente com evidência
```

Durante shadow, impedir que o Core dê baixa produtiva duplicada se o legado ainda é autoridade.

---

## 19. Go-live gates

- ownership por capability definido;
- snapshot preservado;
- dry-run aprovado;
- mappings versionados;
- duplicidades resolvidas/aceitas;
- estoque reconciliado;
- variantes revisadas;
- fiscal validado;
- rollback/fallback;
- backup/restore;
- suporte;
- métricas de reconciliação;
- responsáveis assinam o cutover.

---

## 20. Oportunidade de produto SaaS

A experiência das lojas Mora sugere que o **Migration Workbench** pode virar capability importante do futuro SaaS:

```text
Conectar/importar ERP antigo
→ analisar qualidade
→ mostrar problemas
→ sugerir mappings
→ corrigir em lote
→ dry-run
→ reconciliar
→ migrar
```

Isso reduz o principal atrito de um lojista que já possui milhares de produtos.

Mas não construir uma plataforma universal de ETL antes de resolver bem as fontes reais da Mora.

---

## 21. Perguntas específicas para NOOVA e Lips

- existe API oficial/documentada?
- existe export completo de produtos/estoque/vendas/clientes?
- formato/versionamento?
- backup local?
- banco local ou cache?
- quais IDs são estáveis?
- grade/variante como é representada?
- estoque é por loja/depósito?
- exporta usuários/vendedores?
- exporta movimentos de caixa?
- exporta pagamentos?
- exporta documentos/chaves/XML?
- política contratual de portabilidade?

Perguntar ao fornecedor antes de engenharia reversa.

---

## Relacionados

- [Import/export geral](import-export-migration.md)
- [Baseline de campo](../discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [Estoque](../domain/inventory.md)
- [Catálogo](../commerce/catalog-taxonomy.md)
- [Fiscal](../integrations/fiscal-brazil.md)
- [POS/periféricos](../architecture/pos-device-integration.md)
- [Roadmap](../roadmap/roadmap.md)
