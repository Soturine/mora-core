# SRE, Observabilidade e AIOps

## Baseline

Logs estruturados com `requestId/correlationId`, tenant seguro/redigido, módulo, ação, erro e latência. Nunca logar senha, token, cartão ou conteúdo sensível desnecessário.

## Health

- `/livez`: processo vivo.
- `/readyz`: dependências críticas/config/migrations compatíveis.

Readiness não retorna OK se PostgreSQL crítico estiver indisponível.

## Métricas

Requests, latência, taxa de erro, pool DB, job backlog, integration failures, webhook lag, stock sync lag, uploads, AI jobs/cost, marketplace errors e business health signals úteis.

## Tracing

Adicionar quando fluxos distribuídos/adapters dificultarem diagnóstico. Não instrumentar por cerimônia.

## SLI/SLO

Definir somente quando produção real fornecer objetivos mensuráveis. Exemplos futuros: disponibilidade da API, latência p95 de leitura de catálogo, tempo de propagação de estoque, sucesso de processamento de pedidos.

## AIOps

IA pode auxiliar agrupamento de erros, anomalias, correlação de incidentes, resumo de logs e sugestão de runbook. Não fecha incidentes, executa remediação destrutiva ou altera infraestrutura crítica sem policy/checkpoint humano.

## Alertas

Alertas devem ser acionáveis e vinculados a runbook. Evitar alertar por qualquer ruído. Error budget pode orientar prioridade quando SLOs existirem.
