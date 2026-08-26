# DevOps, DevSecOps e Release Engineering

## Fluxo

`build → test → package → deploy → operate`, automatizado e reproduzível.

## Ambientes

`local`, `staging`, `production` separados. Nunca testar migrations perigosas diretamente em dados de produção.

## CI

Gates graduais: format/lint, typecheck, unit/component, integration conforme mudança, build, security scans aplicáveis. CI assíncrona após push; full suite em milestone/release/blast radius.

## Git

Pequenos commits lógicos. Push remoto real. Antes de tag, confirmar `HEAD == origin/main` e todos os gates no SHA exato.

## Supply chain

Lockfiles; dependências justificadas; Dependabot sem auto-merge; actions oficiais e permissões mínimas; pin por SHA quando adequado; SBOM/provenance em releases relevantes.

## Deploy

Preferir artifacts imutáveis. Estratégia de rollout/rollback definida antes de produção. Feature flags somente quando ajudam rollout seguro e sempre com owner/expiração.

## Migrations

Backward-compatible quando possível; expand/migrate/contract para mudanças arriscadas. Backup não substitui migration segura.

## Secrets

Secret manager; acesso least privilege; rotação; nunca em repo, image, frontend ou CI logs.

## IaC

Infraestrutura relevante deve ser versionada e reproduzível. IaC scanning quando existir. Não introduzir Kubernetes apenas para “ser SaaS”.

## Pós-deploy

Smoke/health funcional, logs/metrics, erro rate e validação de jornada crítica proporcional ao risco.
