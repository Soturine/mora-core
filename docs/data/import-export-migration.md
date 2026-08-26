# Importação, Exportação e Migração

> **Status:** arquitetura planejada para reduzir atrito de entrada/saída do SaaS.

## Objetivo

Permitir que um comércio migre de Bling, planilha ou outro ERP sem redigitar todo o catálogo e permitir que saia do Mora Core com seus dados quando necessário.

## Princípio

Importação é uma **migração controlada**, não `INSERT` em massa.

Exportação é uma capacidade de portabilidade, não um dump interno de banco.

---

# Import pipeline

```text
source
→ upload/connect
→ structural validation
→ detect format/version
→ mapping
→ preview
→ dedupe/conflicts
→ domain validation
→ dry-run
→ user confirmation
→ idempotent job
→ reconciliation
→ report
```

## Fontes candidatas

- Bling API;
- CSV;
- Excel;
- Olist/Tiny/Omie via adapters futuros;
- export de sistema legado;
- cadastro manual.

Começar por fontes reais da operação.

## Staging de import

Dados importados podem passar por staging separado antes de virar canônicos.

Vantagens:

- visualizar erro;
- mapping;
- corrigir coluna;
- evitar metade do catálogo gravado.

## Mapping

Exemplo CSV:

```text
"Código" → sku
"Descrição" → product.name
"Cor" → variant.color
"Tam" → variant.size
```

Salvar template de mapping por organização/formato quando útil.

## Identidade/dedupe

Não deduplicar por nome.

Sinais:

- external provider + external ID;
- SKU;
- GTIN;
- supplier reference;
- mapping confirmado;
- fingerprint cuidadosamente definido.

Conflito deve aparecer ao usuário:

```text
CRIAR NOVO
ATUALIZAR EXISTENTE
IGNORAR
REVISAR
```

Não mesclar automaticamente produtos parecidos por IA.

## Variantes

Import precisa reconstruir corretamente parent/variant. Não transformar cada tamanho em produto separado se a estrutura canônica exige variante, salvo decisão consciente.

## Estoque inicial

Saldo importado gera provenance:

```text
InventoryMovement(OPENING_IMPORT)
```

com source/job/date.

Reimport/retry não duplica saldo.

## Preço/custo

Distinguir:

- preço de venda;
- custo;
- promo;
- moeda;
- vigência.

Campos desconhecidos não viram zero.

## Mídia

Import de URLs externas precisa:

- allowlist/SSRF protection;
- timeout;
- size limit;
- MIME validation;
- download assíncrono;
- checksum;
- provenance.

Não depender para sempre de URL temporária do ERP antigo.

## Clientes/PII

Importar somente com finalidade/base adequada. Preview não deve expor PII para usuário sem permissão.

## Dry-run

Produz relatório sem efetivar:

```text
1.250 produtos analisados
1.100 novos
90 updates
40 conflitos
20 erros
```

Permite corrigir antes de gravar.

## Atomicidade

Import grande provavelmente não cabe em uma transação única. Precisa de estratégia:

- chunks idempotentes;
- checkpoint;
- job status;
- compensation/restart;
- relatório parcial.

Nunca declarar sucesso se 30% falhou sem sinalização.

## Idempotência

Job tem identity/fingerprint. Cada record/chunk pode ter operation key.

Reprocessar não duplica produto/movimento.

## Observabilidade

- rows total;
- processed;
- success;
- skipped;
- conflict;
- failed;
- duration;
- throughput;
- memory;
- error samples sanitizados.

## Budgets

Definir:

- arquivo máximo;
- linhas;
- colunas;
- timeout;
- rows/chunk;
- concurrent jobs por tenant;
- temp storage lifetime.

## Segurança de arquivo

- extensão + MIME real;
- limite;
- parser seguro;
- formula injection em CSV/Excel export/import;
- zip bomb quando aceitar arquivos compactados;
- nenhum macro executado;
- storage isolado;
- malware scan quando apropriado.

---

# Export

## Formatos

Preferir formatos estáveis e documentados:

- CSV para tabelas simples;
- JSON/NDJSON para estruturas;
- ZIP contendo arquivos + manifest quando necessário.

## Manifest

Pode incluir:

```json
{
  "formatVersion": 1,
  "organization": "...",
  "generatedAt": "...",
  "files": []
}
```

## Dados exportáveis

Conforme permissão/retention:

- catálogo;
- variantes;
- categorias;
- preços;
- estoque/movimentos;
- vendas;
- comissão;
- clientes;
- fornecedores;
- mappings;
- mídia manifest.

Fiscal/XML pode ter procedimento próprio.

## Async export

Export grande:

```text
request
→ authorization/step-up
→ job
→ package
→ signed URL curta
→ audit
→ expiry/delete temp
```

## Privacidade

Export de PII exige capability/possibly step-up. Audit registra quem exportou e escopo, não o conteúdo sensível.

## Anti-CSV injection

Ao gerar arquivo aberto em Excel, sanitizar células que começam com `=`, `+`, `-`, `@` conforme threat model/formato para reduzir formula injection.

## Versionamento

Formato de export possui versão e migration guide quando quebrar.

## Round-trip

Quando prometermos import/export simétrico, testar:

```text
export tenant fixture
→ importar em ambiente limpo
→ comparar conceitos/invariantes
```

Não prometer round-trip de dados que não suportamos importar.

---

# Migração de ERP

Projeto de migração precisa de:

1. inventory das fontes;
2. data quality report;
3. mapping;
4. dry-run;
5. reconciliation;
6. cutover plan;
7. freeze window quando necessário;
8. fallback;
9. post-cutover verification.

## Cutover de estoque

Risco de vender enquanto importa. Pode exigir janela/congelamento ou delta sync.

## Migração gradual com Bling

Preferência:

```text
sync read-only
→ compare
→ sites usam Core
→ writes controlados
→ shadow PDV
→ source-of-truth migration
```

Não big-bang sem necessidade.

## Critérios de sucesso

- contagens reconciliadas;
- SKUs/variantes corretos;
- totals financeiros conferidos quando migrados;
- external mappings preservados;
- erros documentados;
- rollback/correção possível.

## Testes

- arquivo malformado;
- encoding;
- duplicado;
- 100k+ rows representativos;
- retry;
- cancel/resume;
- cross-tenant;
- formula injection;
- PII authorization;
- stock opening idempotency;
- export expiry.

## Relacionados

- [Data lifecycle](data-lifecycle.md)
- [Bling](../integrations/bling.md)
- [Onboarding](../saas/platform-and-onboarding.md)
- [Estoque](../domain/inventory.md)
