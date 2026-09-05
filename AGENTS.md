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
- O cliente POS ainda exige ADR/POC: PWA + Local Device Bridge, wrapper desktop ou cliente dedicado são opções; não congelar runtime antes de testar os dois caixas reais.
- API HTTP pragmática, contratos explícitos, validação de entrada e erros consistentes.
- Integrações externas e periféricos locais atrás de ports/adapters.
- Nenhum segredo em cliente, Git, logs ou documentação.

## Domínios principais

`identity`, `organizations`, `catalog`, `inventory`, `purchasing`, `sourcing`, `sales`, `cash`, `commissions`, `customers`, `conversational-commerce`, `commerce`, `storefront`, `media`, `ai`, `integrations`, `reporting`, `audit`, `billing`.

`POS` é uma superfície operacional/delivery boundary que orquestra capacidades dos módulos acima; não deve se tornar uma segunda fonte de verdade para estoque, venda, pagamento ou fiscal.

Módulo não significa microserviço.

## Regras não negociáveis

1. `organization_id`/contexto de tenant nunca vem confiavelmente do cliente; autorização deriva da sessão/membership.
2. AuthN != AuthZ != autorização de objeto != isolamento de tenant.
3. **Busca, recomendação, visual search, embeddings, agent tools e alternativas comerciais são sempre tenant-scoped. Cliente da Organization A nunca recebe produto/listing/estoque da B.**
4. Marketplaces só podem ser apresentados como canal alternativo ao consumidor quando o `ChannelListing` pertence à mesma `Organization`.
5. Estoque é ledger de movimentos + saldo derivado; nunca simples `quantity` editável sem movimento auditável.
6. Dinheiro em unidades inteiras menores (centavos) ou decimal exato conforme ADR; não usar ponto flutuante binário para valores monetários.
7. Venda finalizada é imutável; correções usam cancelamento, estorno, devolução ou evento compensatório.
8. Comissão é ledger/provisão auditável e reversível, não um número mágico no funcionário.
9. IA propõe; sistema determinístico calcula, autoriza, valida e persiste.
10. Produto canônico e variantes são a fonte de verdade; canais externos recebem projeções/adaptações.
11. Fotos originais são preservadas; derivados de IA possuem proveniência e aprovação.
12. Webhooks e comandos sensíveis devem ser idempotentes quando retries forem possíveis.
13. Comprovante visual/PDF de pagamento não é confirmação automática; Payment Provider/webhook/consulta ou revisão humana é autoridade.
14. `CustomerRequest`/`DemandSignal` não são venda. `SourcingRequest` não é promessa de compra até a policy/estado adequado.
15. IA/agent não compra mercadoria autonomamente nem promete prazo/preço de fornecedor sem confirmação determinística/humana.
16. Nenhum dado demonstrativo pode ser apresentado como fato comercial real.
17. Sem microserviços/Kafka/Kubernetes/Redis/CQRS/Event Sourcing sem requisito comprovado.
18. **Domínio não conhece marcas/modelos de scanner, impressora ou gaveta.** Trabalhe com capabilities e adapters.
19. Impressão/reimpressão nunca deve finalizar venda novamente. `printReceipt` é side effect de uma venda já identificada.
20. Impressora térmica não é autoridade fiscal. Emissão/autorização passa pelo `FiscalPort`; impressão é representação/saída.
21. Um Local Device Bridge, se adotado, deve ser estreito: sem shell, SQL, filesystem irrestrito, token de banco ou comandos arbitrários.
22. Não depender exclusivamente de WebUSB/Web Serial para operação crítica sem matriz de compatibilidade comprovada.
23. Qualquer afirmação de compatibilidade do POS precisa ser testada em hardware real de referência ou marcada `não validado`.
24. Migração de NOOVA/Lips/Bling passa por snapshot, staging, mapping, dry-run e reconciliação; anomalias do legado nunca são “corrigidas” silenciosamente.

## Docs obrigatórios por área

Para mudanças em commerce conversacional, leia:

- `docs/commerce/whatsapp-commerce-agent.md`;
- `docs/commerce/customer-demand-and-sourcing.md`;
- `docs/commerce/omnichannel.md`;
- `docs/saas/multitenancy.md`.

Para fiscal/pagamentos:

- `docs/domain/fiscal-and-payments.md`;
- `docs/integrations/fiscal-brazil.md`.

Para compras/sourcing:

- `docs/domain/purchasing-and-suppliers.md`;
- `docs/commerce/customer-demand-and-sourcing.md`.

Para POS/hardware/periféricos:

- `docs/discovery/mora-pos-hardware-baseline-2026-09-05.md`;
- `docs/architecture/pos-device-integration.md`;
- `docs/domain/sales-cash-commissions.md`;
- `docs/domain/fiscal-and-payments.md`;
- `docs/mobile/barcodes-and-scanning.md`;
- `docs/qa/test-strategy.md`.

Para migração dos sistemas atuais:

- `docs/data/import-export-migration.md`;
- `docs/data/mora-legacy-data-migration.md`;
- `docs/integrations/bling.md`;
- `docs/discovery/mora-pos-hardware-baseline-2026-09-05.md`.

## Antes de editar

1. Leia `README.md` e `docs/index.md`.
2. Leia o documento do domínio afetado.
3. Leia ADRs relacionados.
4. Consulte `docs/roadmap/requirements-traceability.md` quando a mudança deriva de requisito discutido.
5. Identifique regra de negócio, invariantes, riscos, estados, permissões, concorrência e critérios de aceitação.
6. Defina explicitamente out-of-scope.
7. Se depender de política externa (Meta, marketplace, SEFAZ, fiscal), revalide documentação oficial atual em vez de confiar em snapshot antigo.
8. Se tocar POS/periférico, verifique o baseline real e não assuma que as duas lojas usam o mesmo equipamento ou legado.
9. Se tocar migração, preserve provenance e trate `unknown` diferente de zero/default.

## Fluxo de trabalho

`explorar → planejar → implementar pequeno lote → teste focado → revisão → commit lógico → push remoto real → acompanhar CI assíncrona`.

Não faça force-push em `main` sem autorização explícita.

## Gates

Quando os scripts existirem, o mínimo por mudança relevante será: format/lint, typecheck, testes focados, integração conforme risco, security checks aplicáveis e build. Full suite apenas em milestone/release ou blast radius justificável.

Mudanças de busca/recomendação precisam incluir testes cross-tenant. Mudanças em pagamento/estoque/webhooks precisam incluir idempotência/concorrência conforme risco.

Mudanças no POS/periféricos exigem, conforme blast radius:

- scanner conhecido/desconhecido/repetido;
- fallback de entrada manual;
- printer unavailable/out-of-paper/reconnect;
- reprint sem duplicar venda;
- bridge indisponível/malicioso;
- operação em pelo menos uma estação real e, antes do piloto, nas duas lojas de referência;
- consumo de CPU/RAM/latência medidos no hardware-alvo;
- contingência/fallback explícitos.

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

Para hardware, “funciona no meu PC” não é evidência suficiente. Registre estação/dispositivo/driver/versão/caso testado.

## Documentação

README = entrada atual. `/docs` = verdade aprofundada. ADR = decisão. Changelog = história. Runbook = operação. Threat model = riscos/controles. Research snapshot = informação externa datada. Baseline de campo = evidência observada, não contrato eterno. Não transforme README em changelog nem snapshot de fornecedor em contrato eterno.

## Agentes e sessões longas

Tarefas pequenas e coerentes. Single-agent por padrão. Se necessário, mantenha handoff/ledger com objetivo, baseline SHA, concluído, decisões, falhas conhecidas, testes verdes e próxima ação.
