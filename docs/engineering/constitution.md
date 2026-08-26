# Engineering Constitution — Mora Core

Esta é a adaptação operacional do padrão permanente de engenharia ao Mora Core. A versão extensa que originou esta constituição prioriza regra de negócio, arquitetura simples com boas fronteiras, segurança, testes por risco, documentação confiável, CI assíncrona, supply chain, observabilidade, resiliência e uso responsável de IA.

## 1. Objetivo

Construir software que resista a auditoria técnica séria e uso real. “Funciona na demo” não é Definition of Done.

## 2. Regra de negócio primeiro

Antes de implementar, identificar atores, objetivos, estados, transições, invariantes, permissões, inputs/outputs, erros, edge cases, concorrência, lifecycle, critérios de aceitação e requisitos não funcionais. Frontend pode validar por UX, mas backend/domínio é autoridade.

## 3. Arquitetura

Escolher a solução mais simples com fronteiras claras. Monólito modular é o default. Buscar coesão alta, acoplamento baixo, contratos claros e dependency inversion. Evitar God Objects, `utils` genérico, serviços gigantes, dependências circulares e microserviços prematuros.

## 4. Dados

Migrations são autoridade do schema. Banco protege invariantes importantes com PK/FK/UNIQUE/NOT NULL/CHECK/transações. Avaliar identity, ownership, lifecycle, uniqueness, retention, deletion, audit, concorrência, versão e proveniência.

## 5. Concorrência e idempotência

Nunca depender apenas de check-then-write. Usar constraints, transações, locks ou optimistic concurrency quando necessário. Webhooks, pagamentos, importações e operações retryable devem ser idempotentes.

## 6. Segurança e privacidade

AuthN, AuthZ, autorização de objeto e isolamento de tenant são conceitos distintos. Aplicar menor privilégio, fail closed, defesa em profundidade, validação, limites de recurso, proteção contra injection/XSS/CSRF/SSRF/IDOR/BOLA, sessões seguras e auditoria. Secrets nunca entram em Git, frontend ou logs.

## 7. APIs

APIs HTTP devem ser resource-oriented quando adequado, com métodos/status corretos, paginação/limites, filtros, idempotência, autorização e contratos de erro consistentes. OpenAPI deve representar contrato real. Não espelhar tabelas diretamente como API. Ações de domínio podem usar comandos explícitos.

## 8. Testes

Escolher por risco: unit, component, integration, contract, DB integration, E2E de jornadas críticas, property/fuzz/mutation/security/migration/recovery/performance quando justificável. Não mockar o comportamento que o teste deveria provar. Smoke precisa provar consequência real. Flaky test é defeito.

## 9. Git e CI

Pequenos batches: teste focado → commit lógico → push remoto real. CI remota trabalha em paralelo. Antes de tag/release, o SHA exato deve estar remoto e verde. Não fazer force-push destrutivo em branches compartilhadas sem autorização.

## 10. Release engineering

Artifact publicado deve ser o mesmo artifact validado: source SHA → tests → artifact → hash → SBOM/provenance quando aplicável → tag → release. Evitar reconstruções divergentes.

## 11. Supply chain

Toda dependência nova precisa justificar necessidade, licença, manutenção, segurança, compatibilidade e custo. Lockfiles. GitHub Actions com permissões mínimas e pinning robusto quando adequado. Usar SAST/SCA/CodeQL/secret scanning/container/IaC scanning conforme a superfície existir.

## 12. Documentação

README é entrada atual, não changelog. Docs atuais descrevem a verdade atual. ADR registra decisão. Runbook registra operação/recovery. Threat model registra risco/controle. Exemplos/comandos/links devem ser verificáveis quando possível.

## 13. Observabilidade, SRE e AIOps

Baseline: logs estruturados, erros acionáveis e correlation IDs. Serviços reais: metrics, liveness/readiness; tracing e SLOs quando a complexidade justificar. AIOps pode auxiliar detecção/correlação, nunca substituir sinais determinísticos ou procedimentos de incidente.

## 14. Resiliência e performance

Timeout, retry/backoff/jitter, circuit breaker, bulkhead, backpressure, outbox/inbox e DLQ entram apenas se o problema existir. Medir antes de otimizar; investigar Big-O, N+1, query plans, I/O e chamadas duplicadas antes de introduzir cache/Redis.

## 15. Frontend e acessibilidade

UI reflete o domínio e separa server state, UI state, form state, URL state e preferências. Acessibilidade é requisito desde o design: semântica, teclado, foco, contraste, labels, screen reader, zoom, reduced motion e touch targets.

## 16. IA

LLMs são bons para propor, resumir, classificar, estruturar e explicar. Sistemas determinísticos calculam, autorizam, validam invariantes e persistem. Output de IA é não confiável. Usar schemas/structured outputs, budgets, proveniência, avaliações e revisão humana proporcional ao risco. Prompt injection não pode ampliar capabilities.

## 17. Agentes de desenvolvimento

Agentes recebem mapa do repo, instruções curtas, ADRs, task spec e gates executáveis. Evitar prompts gigantes repetitivos. Single agent é padrão; paralelismo somente para trabalho realmente independente. Sessões longas usam handoff/ledger.

## 18. Verification != Validation

Verification responde “construímos corretamente?”. Validation responde “construímos a coisa certa?”. Ambos são necessários.

## 19. Auditoria independente

Milestones importantes exigem revisão independente de implementação, arquitetura, segurança, dados, testes, docs, performance e claims. “Testes verdes” ou “agente disse que terminou” não provam completion.

## 20. Definition of Done

Uma mudança está pronta quando a regra funciona, negativos foram considerados, segurança é adequada, testes são significativos, docs/contratos/migrations estão consistentes, não há dead code relevante, push remoto ocorreu e os gates aplicáveis estão verdes no SHA correto.
