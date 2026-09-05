# Plano de Testes — Mora POS e Hardware Real

> **Status:** planejado.  
> **Objetivo:** provar compatibilidade operacional do futuro Mora POS nos equipamentos de referência das lojas Mora antes de qualquer piloto/cutover.

Este plano complementa a [estratégia geral de QA](test-strategy.md), o [baseline de campo](../discovery/mora-pos-hardware-baseline-2026-09-05.md) e a [arquitetura de periféricos](../architecture/pos-device-integration.md).

---

## 1. Princípio

`build passou` não prova que um caixa funciona.

Para POS físico, a evidência precisa incluir hardware real, driver real, dispositivo desconectado, retomada, impressão e fluxo completo de venda.

---

## 2. Matriz mínima de referência

### Mora feminina/familiar

- Windows 11 64-bit observado;
- Intel Core i3-3240 / 6 GB;
- YHD-8200;
- Bematech MP-4200 TH;
- terminal de pagamento ainda a identificar.

### Mora masculina

- Windows 10 64-bit observado;
- Intel Celeron N3350 / 6 GB;
- Knup KP-1026;
- VERDE DYJ-5801 USB 58 mm;
- terminal de pagamento ainda a identificar.

Antes do piloto, registrar também edição/build do Windows, driver, porta, VID/PID quando útil, bridge/client version e configuração de scanner.

---

## 3. CUJ-POS-01 — abrir operação

```text
iniciar estação
→ iniciar cliente/bridge
→ autenticar
→ selecionar contexto permitido
→ abrir/continuar caixa
→ estado pronto
```

Validar:

- cold start;
- restart;
- bridge não iniciado;
- API indisponível;
- sessão expirada;
- relógio incorreto;
- periférico offline.

---

## 4. CUJ-POS-02 — scan → item

```text
barcode
→ capture
→ normalize
→ product/variant lookup
→ item correto
→ carrinho
```

Casos:

- código conhecido;
- desconhecido;
- scan rápido repetido;
- trigger pressionado duas vezes;
- código incompleto;
- sufixo Enter/Tab;
- foco em outro campo;
- scanner desconectado/reconectado;
- fallback digitado;
- pesquisa por descrição/SKU.

Teste deve verificar que uma leitura física não adiciona duas unidades sem intenção.

---

## 5. CUJ-POS-03 — venda simples

```text
scan
→ seller/cashier
→ total
→ pagamento
→ finalize
→ estoque
→ caixa/comissão quando aplicável
→ fiscal
→ recibo
```

Validar idempotência com:

- duplo clique;
- timeout;
- retry;
- refresh/restart após confirmação ambígua.

Uma única intenção não pode virar duas vendas.

---

## 6. CUJ-POS-04 — múltiplos pagamentos

Quando a política real permitir:

- dinheiro + Pix;
- dinheiro + cartão;
- múltiplos cartões se suportado;
- parcelamento;
- crediário somente após regra real definida.

Validar soma exata, arredondamento e cancelamento parcial conforme provider/policy.

---

## 7. CUJ-POS-05 — impressão

Casos:

- impressora pronta;
- sem papel;
- tampa aberta quando detectável;
- USB desconectado;
- driver/spooler parado;
- impressão demora;
- job aceito mas resultado físico desconhecido;
- reimpressão.

Invariante:

```text
reprint != new sale
```

Se a venda está concluída e impressão falha, a UI deve mostrar status acionável e permitir reimprimir o documento existente.

---

## 8. CUJ-POS-06 — gaveta

Depois de confirmar wiring/configuração real:

- abertura por venda em dinheiro;
- sangria;
- suprimento;
- operação administrativa permitida;
- usuário sem permissão;
- bridge offline;
- comando repetido.

Não expor endpoint genérico sem contexto/autorização.

---

## 9. CUJ-POS-07 — sangria/suprimento/fechamento

- abertura;
- movimentos;
- sangria;
- suprimento;
- fechamento normal;
- blind close se configurado;
- divergência;
- aprovação;
- retry;
- reabertura somente se política permitir.

Comparar expected vs counted/reconciled por meio de pagamento.

---

## 10. CUJ-POS-08 — cancelamento/devolução/troca

Depois de Discovery fiscal/comercial:

- cancelamento imediato;
- cancelamento de venda anterior;
- devolução total/parcial;
- troca maior/menor/igual;
- venda já fechada em caixa;
- comissão reversa;
- estoque coerente;
- fiscal coerente.

Não testar apenas UI: verificar efeitos nos módulos autoritativos.

---

## 11. CUJ-POS-09 — queda de Internet

Separar capacidades:

- scan local;
- consulta;
- carrinho;
- venda;
- pagamento;
- fiscal;
- impressão;
- sync.

O teste deve provar exatamente o que continua e o que bloqueia com segurança.

Não usar “offline funciona” como claim genérico.

---

## 12. CUJ-POS-10 — reinício em estado ambíguo

Simular queda/restart:

1. antes de pagamento;
2. durante pagamento;
3. pagamento confirmado mas resposta perdida;
4. durante fiscal;
5. após venda antes de imprimir;
6. durante sync offline.

Após voltar, reconciliation determina o estado antes de repetir efeitos.

---

## 13. Performance budget de balcão

Definir números após baseline medido; inicialmente capturar:

- cold start;
- interactive-ready;
- scan → item visível;
- adicionar item;
- abrir pagamento;
- finalize request;
- print dispatch;
- consumo RAM idle/pico;
- CPU idle/pico.

Medir principalmente na estação Celeron N3350 / 6 GB, que tende a ser o baseline mais restritivo observado.

Não congelar SLA numérico sem medição.

---

## 14. Soak test

Simular um turno:

- centenas/milhares de scans representativos;
- múltiplas vendas;
- impressões;
- login/logout quando aplicável;
- periférico reconectado;
- janela longa aberta.

Observar:

- crescimento de memória;
- handles/processos;
- travamentos;
- fila de impressão;
- latência degradando;
- reconexões.

---

## 15. Device Bridge security tests

Se houver bridge:

- request de origem não autorizada;
- request sem auth/nonce quando aplicável;
- payload inválido;
- payload enorme;
- path traversal;
- command injection;
- tentativa de shell/raw command;
- acesso pela LAN;
- replay;
- rate abuse;
- downgrade/update inválido;
- versão incompatível.

O resultado esperado é fail-closed sem bloquear recuperação administrativa legítima.

---

## 16. Atualização e rollback

Testar:

```text
versão N
→ update N+1
→ restart
→ devices ainda funcionam
→ rollback N se necessário
```

Também:

- energia cai durante update;
- pacote corrompido;
- assinatura inválida;
- disco cheio;
- bridge/client version skew.

---

## 17. Compatibilidade Windows

Registrar por estação:

- edição/build;
- patches;
- arquitetura;
- TPM/Secure Boot quando relevante;
- driver;
- spooler;
- USB power management;
- política de usuário/admin.

Uma aplicação funcionar em uma instalação específica não prova suporte oficial do SO/hardware pelo fabricante.

---

## 18. Evidência de teste

Para cada execução física:

```text
Data
Store/workstation alias
OS build
POS SHA/version
Bridge version
Device model
Driver/port
Scenario
Expected
Actual
Duration/metrics
Logs/correlationId
Pass/Fail
Known issue
```

Não registrar PII de cliente em screenshot/log de QA.

---

## 19. Shadow pilot

Antes do cutover:

```text
legado = produção
Mora POS = shadow
```

Comparar:

- itens;
- totais;
- vendedor/caixa;
- pagamentos;
- estoque projetado;
- comissão;
- fiscal state;
- tempo de atendimento.

Não dar baixa dupla em estoque/financeiro.

---

## 20. Gate para piloto produtivo

- CUJs críticos verdes;
- duas estações de referência validadas;
- periféricos identificados;
- fiscal/payment ownership definido;
- backup/fallback do legado disponível;
- suporte/runbook;
- rollback;
- observabilidade;
- usuário real treinado;
- falhas conhecidas registradas;
- nenhum claim de compatibilidade além do que foi testado.

---

## Relacionados

- [QA geral](test-strategy.md)
- [Baseline real](../discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [POS/periféricos](../architecture/pos-device-integration.md)
- [Vendas/caixa](../domain/sales-cash-commissions.md)
- [Fiscal](../domain/fiscal-and-payments.md)
- [Segurança](../security/security-architecture.md)
