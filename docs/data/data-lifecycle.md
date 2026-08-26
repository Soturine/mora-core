# Data Lifecycle, Retenção e Recuperação

## Classificação

- **Autoritativo persistente:** vendas, movimentos, usuários, catálogo, configurações.
- **Derivado persistente:** thumbnails, projeções, índices, relatórios materializados.
- **Cache regenerável:** CDN/cache de consulta.
- **Temporário:** upload em processamento, jobs transitórios.

## Representações

Para cada dado considerar banco, object storage, derivados, cache, search index, analytics, exports e backups.

## Deleção

Excluir row não significa excluir dado. Offboarding/LGPD precisa mapear destino de cada representação, retenção legal e backup. Dados fiscais/financeiros podem ter obrigações legais de retenção; validar com profissional competente.

## Proveniência

Importações, IA e integrações devem registrar origem, timestamp, versão/formato e identificadores suficientes para auditoria.

## Exportabilidade

Cliente SaaS deve conseguir exportar dados próprios em formatos documentados. Evitar lock-in baseado em aprisionamento de dados.

## Mídia

Original não é substituído por IA. Derivados referenciam `sourceAssetId`, transformação, provider/model version quando aplicável e aprovação.
