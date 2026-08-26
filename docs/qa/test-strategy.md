# Estratégia de QA e Testes

## Objetivo

Provar comportamento por risco, não maximizar quantidade de testes.

## Pirâmide pragmática

- Muitos unit/component para regras pequenas.
- Integração real para PostgreSQL, contracts e módulos críticos.
- Poucos E2E para jornadas críticas.
- Contract tests para adapters e storefront/API.

## Jornadas críticas

1. criar produto + variantes + entrada de estoque;
2. vender variante → estoque baixa uma vez → comissão provisiona → caixa registra;
3. devolução → estoque/financeiro/comissão ajustam corretamente;
4. inventário móvel → sincronização sem duplicidade;
5. pedido marketplace → reserva/baixa → demais canais recebem saldo;
6. tenant A nunca acessa tenant B;
7. fechamento de caixa com divergência;
8. backup restore funcional em milestone de produção.

## Tipos especiais

Property tests para invariantes de estoque/dinheiro; mutation/negative-control em regras críticas; fuzz/adversarial em parsers/uploads/APIs; migration tests; recovery tests; performance tests com budgets reais.

## Anti-fake-smoke

Smoke deve produzir/consultar estado real. Ex.: stack sobe, migration aplicada, write/read sintético funciona, authorization denial funciona e estado persiste após restart quando aplicável.

## Flakiness

Proibido mascarar com sleep/retry cego. Esperar estado com timeout, clocks/seeds controlados e fixtures isoladas.

## QA manual

Mobile: 320/360/390/430px e dispositivos reais quando câmera/scanner/gestos forem críticos. Web: teclado, foco, zoom, leitores de tela, responsividade e erros reais.

## Evidência

Relatório de conclusão deve listar comando → resultado, cenários manuais, riscos não validados e SHA testado.
