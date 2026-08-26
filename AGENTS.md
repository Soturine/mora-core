# AGENTS.md — Mora Core

Este arquivo é o mapa operacional para agentes de desenvolvimento. Ele complementa, mas não substitui, a documentação canônica em `/docs`.

## Princípio

Não otimizar para parecer sofisticado. Otimizar para resistir a uma auditoria séria.

Código gerado por IA é rascunho não confiável até revisão e prova.

## Estado atual

O repositório é `documentation-first`. Não invente implementação ausente. Use os status: `implementado`, `parcial`, `experimental`, `planejado`, `adiado`, `não validado`.

## Arquitetura-alvo

- Monólito modular como padrão inicial.
- PostgreSQL como banco transacional principal quando a implementação começar.
- Object storage para mídia.
- Jobs/filas somente onde processamento assíncrono for necessário.
- React/TypeScript para web; React Native + Expo é a direção preferida para mobile, sujeita a ADR.
- API HTTP pragmática, contratos explícitos, validação de entrada e erros consistentes.
- Integrações externas atrás de ports/adapters.
- Nenhum segredo em cliente, Git, logs ou documentação.

## Domínios principais

`identity`, `organizations`, `catalog`, `inventory`, `purchasing`, `sales`, `cash`, `commissions`, `customers`, `commerce`, `storefront`, `media`, `ai`, `integrations`, `reporting`, `audit`, `billing`.

Módulo não significa microserviço.

## Regras não negociáveis

1. `organization_id`/contexto de tenant nunca vem confiavelmente do cliente; autorização deriva da sessão/membership.
2. AuthN != AuthZ != autorização de objeto != isolamento de tenant.
3. Estoque é ledger de movimentos + saldo derivado; nunca simples `quantity` editável sem movimento auditável.
4. Dinheiro em unidades inteiras menores (centavos); não usar ponto flutuante para valores monetários.
5. Venda finalizada é imutável; correções usam cancelamento, estorno, devolução ou evento compensatório.
6. Comissão é ledger/provisão auditável e reversível, não um número mágico no funcionário.
7. IA propõe; sistema determinístico calcula, autoriza, valida e persiste.
8. Produto canônico e variantes são a fonte de verdade; canais externos recebem projeções/adaptações.
9. Fotos originais são preservadas; derivados de IA possuem proveniência e aprovação.
10. Webhooks e comandos sensíveis devem ser idempotentes quando retries forem possíveis.
11. Nenhum dado demonstrativo pode ser apresentado como fato comercial real.
12. Sem microserviços/Kafka/Kubernetes/Redis/CQRS/Event Sourcing sem requisito comprovado.

## Antes de editar

1. Leia `README.md` e `docs/index.md`.
2. Leia o documento do domínio afetado.
3. Leia ADRs relacionados.
4. Identifique regra de negócio, invariantes, riscos, estados, permissões, concorrência e critérios de aceitação.
5. Defina explicitamente out-of-scope.

## Fluxo de trabalho

`explorar → planejar → implementar pequeno lote → teste focado → revisão → commit lógico → push remoto real → acompanhar CI assíncrona`.

Não faça force-push em `main` sem autorização explícita.

## Gates

Quando os scripts existirem, o mínimo por mudança relevante será: format/lint, typecheck, testes focados, integração conforme risco, security checks aplicáveis e build. Full suite apenas em milestone/release ou blast radius justificável.

## Definition of Done

- comportamento correto;
- caminhos negativos;
- autorização/isolamento adequados;
- testes capazes de detectar defeitos;
- docs/contratos/migrations atualizados;
- sem dead code relevante;
- push remoto confirmado;
- gates requeridos verdes no SHA correto;
- claims finais compatíveis com evidência.

## Documentação

README = entrada atual. ADR = decisão. Changelog = história. Runbook = operação. Threat model = riscos/controles. Não transforme README em changelog.

## Agentes e sessões longas

Tarefas pequenas e coerentes. Single-agent por padrão. Se necessário, mantenha handoff/ledger com objetivo, baseline SHA, concluído, decisões, falhas conhecidas, testes verdes e próxima ação.
