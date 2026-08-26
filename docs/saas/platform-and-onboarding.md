# Plataforma SaaS e Onboarding

## Objetivo

Permitir que um comércio comece a usar o Mora Core sem intervenção manual do time da plataforma para cada cliente.

## Fluxo de onboarding

```text
Criar conta
→ criar organização
→ informar estrutura legal/marcas
→ criar loja(s)/estoque(s)
→ importar ou cadastrar catálogo
→ convidar equipe
→ configurar permissões/comissões
→ conectar integrações
→ opcionalmente criar site
→ validar operação
→ entrar em produção
```

## Organização com múltiplos negócios

Uma `Organization` pode conter múltiplas `LegalEntity`, `Brand`, `Store`, `StockLocation`, `Website` e `SalesChannel`. Isso permite grupos com várias lojas/marcas sem misturar identidade legal, estoque ou relatórios.

## Provisionamento

Provisionamento deve ser idempotente. Falha parcial não pode deixar tenant sem owner, sem configuração básica ou com recursos órfãos. Preferir workflow compensável e estado explícito de onboarding.

## Importação

Oferecer caminhos como Bling, CSV/Excel e cadastro manual. Import nunca escreve cegamente: preview, validação, mapeamento, dedupe e relatório de erro.

## Site

Cliente sem site pode iniciar com subdomínio gerenciado e depois conectar domínio próprio. DNS/certificado/publicação precisam de status operacional explícito.

## Convites

Convites expiram, são de uso único e vinculados a organization/role/escopo. Não permitir aceitação para tenant diferente.

## Trial/ativação

Trial, planos e limites pertencem ao billing/entitlements; não hardcodar no domínio operacional.

## Offboarding

Antes do encerramento: export, revogação de integrações, política de retenção e exclusão conforme contrato/lei. Ver [data lifecycle](../data/data-lifecycle.md).
