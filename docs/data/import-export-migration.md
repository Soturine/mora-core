# Importação, Exportação e Migração

## Objetivo

Reduzir atrito para entrada/saída do SaaS e permitir migração segura de ERPs/planilhas.

## Pipeline de import

Upload → validação estrutural → preview → mapping → dedupe → validação de domínio → dry-run → confirmação → job idempotente → relatório.

## Identidade/dedupe

Não deduplicar produto apenas por nome. Considerar external IDs, SKU, GTIN/barcode e mapping confirmado. Conflitos devem ser mostrados ao usuário.

## Migração de estoque

Saldo inicial deve gerar movimento de abertura/importação auditável, não escrever `InventoryBalance` diretamente.

## Export

Formato documentado e versionado para produtos, variantes, estoque, vendas, clientes, fornecedores e manifest de mídia conforme permissões/retention.

## Privacidade

Export de PII exige autorização, logging e possibly step-up. Arquivos temporários expiram e usam URLs assinadas.

## Reprocessamento

Import job usa chave idempotente/fingerprint para evitar duplicação por retry.
