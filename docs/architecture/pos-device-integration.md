# Integração do POS com Periféricos Locais

> **Status:** arquitetura planejada, informada pelo baseline de campo de 05/09/2026.  
> **Objetivo:** permitir que o futuro Mora POS opere leitores, impressoras, gavetas e terminais sem acoplar o domínio a um fabricante, navegador ou modelo específico.

Ver primeiro o [baseline real das lojas Mora](../discovery/mora-pos-hardware-baseline-2026-09-05.md).

---

## 1. Problema

O Mora Core precisa funcionar em operação física real. Nas duas lojas de referência foram observados:

- dois PDVs legados diferentes;
- computadores com capacidades diferentes;
- leitores de código de barras de marcas/modelos diferentes;
- impressoras térmicas diferentes;
- terminais de pagamento físicos;
- Windows em versões diferentes.

Logo, `POS` não pode significar apenas “uma página web com um formulário de venda”.

Precisamos separar:

```text
regra de negócio
!=
interface do caixa
!=
integração com periférico
!=
integração fiscal
!=
integração de pagamento
```

---

## 2. Princípio arquitetural

O domínio trabalha com **capacidades**, não marcas.

```text
barcode_input
receipt_print
label_print
cash_drawer
payment_terminal
weighing_scale?       # futuro, se um cliente precisar
customer_display?     # futuro
```

Não espalhar código como:

```text
if printer == "Bematech"
if scanner == "Knup"
if store == "Mora Masculina"
```

pelo domínio ou casos de uso.

Fabricante/modelo pertence ao adapter/driver/configuração da instalação.

---

## 3. Boundary do POS

```text
                         MORA CORE
                            │
                     HTTP/API segura
                            │
                        Mora POS
                     /              \
                    /                \
          regras/estado remoto        integração local
                  │                         │
            API Mora Core              Device Bridge?
                                             │
                         ┌───────────────────┼───────────────────┐
                         │                   │                   │
                      scanner            impressora           gaveta
                                                                │
                                                     terminal/TEF adapter
                                                     quando aplicável
```

### Mora Core API

É autoridade de:

- autorização;
- catálogo/preço autorizado;
- venda;
- estoque;
- caixa;
- comissão;
- payment state;
- fiscal state;
- idempotência;
- audit.

### POS client

É responsável por:

- interação rápida de balcão;
- captura do scan/input;
- apresentação do carrinho/estado;
- coordenação da UX de pagamento;
- solicitação de impressão;
- estado de periféricos;
- modo degradado explicitamente permitido.

### Device Bridge

Quando necessário, é um processo local **estreito**, responsável apenas por conversar com periféricos/driver/spooler do SO.

Não é um “backend escondido” do Mora Core.

---

## 4. Scanner

### Caminho preferido quando suportado

Muitos leitores USB operam como `keyboard wedge`/HID e digitam o código como teclado. Se os equipamentos atuais forem confirmados nesse modo, é a integração inicial preferida por simplicidade e compatibilidade.

```text
scan
→ sequência de caracteres
→ terminador configurado
→ buffer
→ normalização
→ lookup
```

### Regras de captura

- aceitar EAN/GTIN/barcode interno conforme policy;
- limitar comprimento;
- rejeitar caracteres inesperados quando o contexto exigir;
- debounce contra leitura repetida involuntária;
- não somar item duas vezes por duplicidade de eventos do dispositivo;
- aceitar entrada manual/pesquisa como fallback;
- não depender de foco frágil em um input invisível sem recuperação;
- feedback sonoro/visual apropriado;
- separar `scan captured` de `item added`.

### Scanner desconhecido

```text
barcode não encontrado
→ nenhuma alteração crítica automática
→ pesquisar novamente / iniciar cadastro autorizado / informar operador
```

Scan não equivale a autorização.

---

## 5. Impressão de recibo

### Impressão não é venda

A venda é concluída no domínio. A impressão é um efeito operacional relacionado.

```text
Sale COMPLETED
→ receipt representation
→ PrintJob
→ device adapter/spooler
```

Falha da impressora após a venda **não pode criar uma segunda venda** quando o operador clicar em “imprimir novamente”.

### Reimpressão

Reimpressão precisa referenciar o mesmo documento/recibo:

```text
reprint(receiptId)
```

não:

```text
finalizeSaleAgain()
```

Audit pode registrar reimpressões sensíveis conforme necessidade operacional/fiscal.

### Estado de impressão

Estados conceituais possíveis:

```text
PENDING
SENT_TO_DEVICE
PRINTED_CONFIRMED?       # somente se realmente confirmável
FAILED
UNKNOWN
```

Muitos dispositivos/spoolers não fornecem prova física perfeita de que o papel saiu. Não inventar `PRINTED` quando só sabemos que o job foi aceito pelo sistema operacional.

---

## 6. Impressão de etiqueta

Etiquetas pertencem ao fluxo de catálogo/recebimento, mas usam o mesmo princípio de adapter.

```text
LabelTemplate
+ Variant facts
+ internal barcode
→ render
→ PrintJob
→ LabelPrinterAdapter
```

O template pode variar por organização/impressora/tamanho sem alterar `ProductVariant`.

Não gerar GTIN oficial arbitrariamente. Para itens sem GTIN, usar identificador interno conforme [Barcodes e scanning](../mobile/barcodes-and-scanning.md).

---

## 7. Gaveta de dinheiro

A gaveta não deve abrir por endpoint genérico ou comando arbitrário.

A capability é estreita:

```text
openCashDrawer(reason/context)
```

A política de negócio decide quando a ação é permitida, por exemplo:

- venda em dinheiro concluída;
- sangria/suprimento autorizado;
- abertura administrativa com permissão/motivo.

O Device Bridge só executa o pulso/comando técnico depois de receber operação permitida.

A gaveta pode estar conectada à impressora em alguns setups; isso é detalhe de adapter.

---

## 8. Pagamento e TEF

`PaymentTerminalAdapter` é separado de `PrinterAdapter`.

```text
POS
→ PaymentPort
→ TEF/PSP/adquirente
→ resultado/reconciliation
```

Não assumir integração apenas porque há uma maquininha no balcão.

Antes do adapter real, confirmar:

- adquirente;
- terminal/modelo;
- TEF vs POS desacoplado;
- protocolo/SDK;
- Pix;
- parcelamento;
- cancelamento;
- timeout ambíguo;
- conciliação.

Nunca armazenar PAN/CVV completo no Mora Core.

---

## 9. Fiscal não é impressora

A impressora térmica de balcão **não é a autoridade fiscal**.

```text
FiscalDocument
→ FiscalPort
→ provider/SEFAZ
```

Depois de autorizado, o POS pode imprimir ou disponibilizar a representação permitida.

```text
fiscal authorization
→ printable representation
→ receipt printer
```

Separar:

- emissão/autorização fiscal;
- DANFE/representação;
- comprovante comercial;
- comprovante de pagamento.

Essa separação evita acoplar o domínio fiscal a uma impressora específica.

---

## 10. Local Device Bridge

### Quando usar

Avaliar quando browser/PWA sozinho não oferece acesso confiável aos periféricos necessários.

### Contrato estreito

Exemplo:

```text
GET  /health
GET  /devices
POST /print/receipt
POST /print/label
POST /cash-drawer/open
```

A API concreta depende de ADR/POC.

### Nunca expor

- shell genérico;
- execução arbitrária;
- SQL;
- caminho de arquivo arbitrário;
- secrets do Mora Core;
- token de banco;
- token de marketplace;
- leitura irrestrita do filesystem;
- endpoint “raw command” público por conveniência.

### Binding

Preferir comunicação local explícita:

- `127.0.0.1`/IPC local;
- não escutar rede LAN/Internet por padrão;
- origin/app allowlist;
- autenticação local apropriada;
- nonce/request correlation quando necessário;
- limits e timeouts.

### Atualização

Se distribuído como software:

- binário assinado quando a plataforma permitir;
- update assinado/verificado;
- rollback;
- versão reportada;
- health/diagnostic export sanitizado;
- least privilege;
- inventory de instalação.

---

## 11. WebUSB e Web Serial

WebUSB e Web Serial podem ser úteis para casos controlados, mas a documentação MDN os classifica como recursos de disponibilidade limitada e dependentes de contexto seguro/permissão do usuário.

Portanto:

- podem ser usados como progressive enhancement/POC;
- não devem ser a única estratégia para uma capability crítica sem matriz de compatibilidade comprovada;
- não substituir driver/spooler maduro somente para “ser web”.

Fontes:

- https://developer.mozilla.org/en-US/docs/Web/API/WebUSB_API
- https://developer.mozilla.org/en-US/docs/Web/API/Web_Serial_API

---

## 12. Web/PWA vs desktop wrapper

A escolha deve ser feita por ADR depois de POC nas duas lojas.

### Opção A — Web/PWA + bridge local

Vantagens potenciais:

- UI atualizável centralmente;
- domínio/API compartilhados;
- bridge pequeno.

Riscos:

- duas partes para operar;
- version skew UI ↔ bridge;
- browser policies;
- suporte local.

### Opção B — desktop wrapper leve

Vantagens potenciais:

- integração local mais direta;
- distribuição controlada;
- melhor acesso a SO/periféricos.

Riscos:

- updater/distribuição;
- superfície local maior;
- bundle/memória;
- segurança do runtime.

### Opção C — desktop dedicado

Somente se os requisitos mostrarem que PWA/wrapper/bridge não atendem com confiabilidade aceitável.

### Critério

Não escolher por preferência tecnológica. Medir no hardware de referência:

- memória idle/pico;
- CPU;
- startup;
- scan-to-item latency;
- print latency;
- recovery após perda de dispositivo;
- update;
- offline;
- segurança;
- facilidade de suporte.

---

## 13. Registro de dispositivos

Modelo conceitual, se necessário:

```text
DeviceInstallation
├── id
├── organizationId
├── storeId
├── workstationId
├── type
├── vendor?
├── model?
├── transport?
├── driverVersion?
├── bridgeVersion?
├── capabilities[]
├── status
└── lastSeenAt
```

Não usar `vendor/model` como autorização.

`capabilities` são o contrato útil para a aplicação.

---

## 14. Offline e contingência

Não prometer “PDV offline” apenas porque o cliente guarda um carrinho local.

Precisamos separar:

```text
consulta local
venda local
pagamento local
caixa local
fiscal em contingência
sync posterior
```

Cada capacidade possui risco diferente.

Qualquer outbox local deve usar `operationId` estável e replay idempotente.

Estado ambíguo após pagamento/fiscal exige reconciliation antes de repetir efeito.

---

## 15. Threat model do bridge/periféricos

Ameaças relevantes:

- site malicioso chamando localhost;
- bridge exposto à LAN;
- command injection;
- impressão abusiva;
- abertura indevida da gaveta;
- spoof de dispositivo;
- update malicioso;
- log contendo PII/segredo;
- operador sem permissão disparando ação privilegiada;
- workstation comprometida.

Controles:

- bind local;
- autenticação/allowlist;
- schema estrito;
- command allowlist;
- rate limits;
- assinatura/update seguro;
- server-side authorization para operação de negócio;
- logs sanitizados;
- version/health telemetry;
- least privilege.

---

## 16. Compatibilidade mínima de piloto Mora

O primeiro spike do POS deve testar, no mínimo:

### Loja feminina/familiar

- estação Core i3-3240 / 6 GB;
- leitor YHD-8200;
- Bematech MP-4200 TH.

### Loja masculina

- estação Celeron N3350 / 6 GB;
- leitor Knup KP-1026;
- impressora VERDE DYJ-5801 USB 58 mm.

Em ambos:

- scan conhecido/desconhecido;
- leitura repetida;
- impressão;
- reimpressão;
- sem papel/offline;
- desconectar/reconectar;
- restart da estação;
- atualização do cliente/bridge;
- perda de Internet;
- fallback manual.

Os computadores atuais são **baseline de migração/piloto**, não garantia de requisitos mínimos eternos do SaaS.

---

## 17. Observabilidade

Métricas operacionais úteis, sem conteúdo sensível:

```text
device_connected
scanner_read_success/failure
print_job_success/failure/unknown
print_latency
bridge_version
bridge_health
cash_drawer_action_count
payment_adapter_health
```

Não enviar barcode completo, recibo completo ou PII para telemetria genérica sem necessidade.

---

## 18. Gates antes do piloto/shadow

- inventário real de drivers/portas;
- POC nos dois computadores;
- scanner testado como HID/keyboard wedge ou adapter definido;
- duas impressoras testadas;
- reprint idempotente;
- fiscal separado da impressão;
- fluxo de pagamento mapeado;
- bridge threat model revisado;
- auto-update/rollback testado se houver bridge/wrapper;
- modo degradado definido;
- suporte/runbook;
- métricas e diagnóstico;
- operação atual continua como fallback no shadow.

---

## 19. Decisões que exigem ADR

1. forma do cliente POS: PWA + bridge, wrapper ou desktop;
2. protocolo/packaging do Device Bridge;
3. estratégia de impressão;
4. offline/contingência do POS;
5. payment/TEF adapter;
6. atualização/distribuição do cliente local;
7. baseline mínimo de SO/hardware no momento do go-live.

---

## Relacionados

- [Baseline de campo](../discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [Arquitetura do sistema](system-architecture.md)
- [Vendas, caixa e comissões](../domain/sales-cash-commissions.md)
- [Fiscal e pagamentos](../domain/fiscal-and-payments.md)
- [Barcodes](../mobile/barcodes-and-scanning.md)
- [Segurança](../security/security-architecture.md)
- [QA](../qa/test-strategy.md)
