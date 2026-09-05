# Rastreabilidade do Levantamento de Campo — 05/09/2026

> **Objetivo:** relacionar evidência observada nas lojas Mora às decisões/documentos atualizados, sem substituir a matriz geral de requisitos.

| Evidência/achado | Implicação | Documento canônico |
| --- | --- | --- |
| Loja feminina/familiar usa NOOVA | há mais de um legado e ownership precisa ser mapeado | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md), [Migração](../data/mora-legacy-data-migration.md) |
| Loja masculina usa Lips Control | migração não pode assumir uma única fonte | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md), [Riscos](risks-and-open-questions.md) |
| PDVs exibem versão `4.01.00.134` | snapshot operacional identificável; não implica compatibilidade/API comum | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md) |
| Caixa `001` feminina e `002` masculina observados | `CashRegister`/store context precisam ser explícitos; números atuais não viram IDs canônicos | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md), [Vendas/caixa](../domain/sales-cash-commissions.md) |
| NOOVA mostra vendedor e cliente | confirma importância de identidade/atendimento no fluxo de balcão | [Módulos](../product/modules.md), [Vendas/caixa](../domain/sales-cash-commissions.md) |
| NOOVA mostra dinheiro, crediário, crédito, débito e Pix | Payment deve ser flexível; regras reais ainda precisam Discovery | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md), [Fiscal/pagamentos](../domain/fiscal-and-payments.md) |
| NOOVA mostra sangria e suprimento | `CashMovement` é requisito real, não apenas benchmark | [Vendas/caixa](../domain/sales-cash-commissions.md), [Roadmap](roadmap.md) |
| NOOVA mostra cancelamento/últimas vendas | correções de venda precisam de fluxo explícito e auditável | [Vendas/caixa](../domain/sales-cash-commissions.md), [QA POS](../qa/pos-hardware-test-plan.md) |
| NOOVA mostra Central de NFC-e | fiscal está presente no fluxo atual, mas ownership não é conhecido | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md), [Fiscal Brasil](../integrations/fiscal-brazil.md), [Riscos](risks-and-open-questions.md) |
| NOOVA mostra gerenciamento do TEF | TEF precisa Discovery; presença de menu não prova integração ativa | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md), [Fiscal/pagamentos](../domain/fiscal-and-payments.md) |
| 5.968 registros de produto observados | migração exige pipeline robusto e data profiling | [Migração dos legados](../data/mora-legacy-data-migration.md) |
| vários itens `Sem Categoria` | taxonomia do legado não pode ser importada cegamente | [Migração](../data/mora-legacy-data-migration.md), [Taxonomia](../commerce/catalog-taxonomy.md) |
| saldos negativos/fracionários em `UN` | saldo legado precisa staging/reconciliação; não arredondar silenciosamente | [Migração](../data/mora-legacy-data-migration.md), [Riscos](risks-and-open-questions.md) |
| YHD-8200 na feminina | scanner precisa adapter/capability e teste real | [POS/periféricos](../architecture/pos-device-integration.md), [QA POS](../qa/pos-hardware-test-plan.md) |
| Knup KP-1026 na masculina | POS não pode ser acoplado a um único scanner | [POS/periféricos](../architecture/pos-device-integration.md), [QA POS](../qa/pos-hardware-test-plan.md) |
| Bematech MP-4200 TH na feminina | impressão precisa adapter e reprint idempotente | [POS/periféricos](../architecture/pos-device-integration.md) |
| VERDE DYJ-5801 USB 58 mm na masculina | matriz de impressão precisa cobrir equipamento diferente | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md), [QA POS](../qa/pos-hardware-test-plan.md) |
| DYJ-5801 indica saída de cash drawer | gaveta pode ser acionada via impressora, mas uso real deve ser validado | [POS/periféricos](../architecture/pos-device-integration.md) |
| PCs têm i3-3240/6 GB e N3350/6 GB | runtime do POS precisa POC em hardware modesto antes de ADR | [Arquitetura](../architecture/system-architecture.md), [Roadmap](roadmap.md), [QA POS](../qa/pos-hardware-test-plan.md) |
| Windows 10 observado na masculina | lifecycle/patching do endpoint precisa verificação | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md), [Riscos](risks-and-open-questions.md) |
| Windows 11 observado em hardware antigo na feminina | elegibilidade/TPM/Secure Boot/build/driver precisam verificação, sem inferência pela foto | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md) |
| terminais físicos de pagamento presentes | não assumir TEF integrado; descobrir adquirente/protocolo | [Baseline](../discovery/mora-pos-hardware-baseline-2026-09-05.md), [Riscos](risks-and-open-questions.md) |
| duas impressoras e dois scanners diferentes | domínio por capability, adapters por instalação | [Módulos](../product/modules.md), [POS/periféricos](../architecture/pos-device-integration.md) |
| operação atual depende de software desktop Windows | PWA vs bridge vs wrapper vs desktop deve ser ADR/POC, não preferência | [Arquitetura](../architecture/system-architecture.md), [POS/periféricos](../architecture/pos-device-integration.md) |
| WebUSB/Web Serial têm disponibilidade limitada | não usá-los como única dependência crítica sem matriz comprovada | [POS/periféricos](../architecture/pos-device-integration.md) |
| sistemas atuais precisam continuar enquanto Core amadurece | shadow/piloto/fallback em vez de big-bang | [Roadmap](roadmap.md), [Migração](../data/mora-legacy-data-migration.md) |

## Regras de interpretação

1. A tabela registra **evidência → requisito/decisão**, não feature implementada.
2. Uma tela observada prova que a UI existe no legado; não prova API, contrato, estado interno ou comportamento em todos os cenários.
3. Marca/modelo de periférico é dado de instalação, não regra de domínio.
4. Dados pessoais e valores comerciais visíveis nas fotografias não entram na documentação técnica.
5. Quando a evidência for insuficiente, manter `não validado` e criar Discovery/POC.

## Relacionados

- [Matriz geral de requisitos](requirements-traceability.md)
- [Auditoria de 05/09/2026](../audit/repository-documentation-audit-2026-09-05.md)
- [Cobertura documental](documentation-coverage.md)
