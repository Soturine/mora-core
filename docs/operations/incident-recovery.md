# Incidentes, Backup e Disaster Recovery

## Ciclo de incidente

Detectar → classificar → conter → preservar evidência → recuperar → validar → comunicar → postmortem sem culpabilização → melhorar controles.

## Severidade

Definir política antes de produção. Exemplos críticos: vazamento cross-tenant, venda/estoque corrompido, cobrança duplicada, indisponibilidade ampla, perda de dados ou comprometimento de credencial privilegiada.

## Backups

Backups automáticos, criptografados e com retenção definida. Para PostgreSQL, considerar PITR quando produção justificar.

## Restore testado

Backup só é confiável se houver exercício de restore: restaurar → aplicar/validar migrations → subir app → verificar invariantes/jornada crítica.

## RPO/RTO

Não inventar números antes de requisitos de negócio. Registrar metas explícitas antes do go-live.

## Runbooks futuros

- DB indisponível;
- marketplace/webhook backlog;
- vazamento de secret;
- tenant data exposure;
- stock divergence;
- fiscal provider outage;
- failed migration;
- object storage/media incident.
