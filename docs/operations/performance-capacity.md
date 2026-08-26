# Performance, Capacidade e FinOps

## Princípio

Medir antes de otimizar.

## Investigar primeiro

Big-O, N+1, query plans, índices, serialização, payloads, network roundtrips, uploads, image processing, job concurrency, alocações e I/O.

## Budgets

Definir com benchmark/uso real: tamanho de upload, imagens por produto, variantes por produto, batch import, pagination, response bytes, timeout DB/HTTP, jobs simultâneos, tokens/IA e custo por tenant.

## Catálogo público

Cache/CDN é apropriado para leitura pública; estoque/preço precisam de freshness contract. Não cachear dado crítico sem estratégia de invalidação.

## Fotos

Original em object storage; derivados WebP/AVIF/thumbnails; lazy loading; não processar toda galeria em request síncrono.

## Escala

Vertical/horizontal scaling do monólito é preferível inicialmente. Redis, filas especializadas, read replicas e microserviços exigem evidência.

## FinOps

Monitorar storage, egress, DB, jobs e especialmente IA por tenant/feature. Custos de IA precisam de quotas/entitlements e dashboards antes de SaaS amplo.
