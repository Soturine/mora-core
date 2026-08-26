# Fiscal e Pagamentos

> **Status:** arquitetura e estratégia de risco. Regras fiscais, meios de pagamento e provedores finais dependem de Discovery, contador e documentação oficial vigente.

## Por que é uma área separada

Fiscal e pagamentos têm consequências legais/financeiras. Uma implementação incorreta pode impedir venda, duplicar cobrança, emitir documento errado ou criar passivo.

O Mora Core não deve transformar “queremos substituir o ERP” em “vamos implementar protocolo fiscal brasileiro completo do zero”.

---

# 1. Estratégia fiscal

## Direção inicial

Manter ERP/provedor existente como autoridade fiscal enquanto o Core aprende e controla catálogo/estoque/operação.

Evolução possível:

```text
Mora Core Sale/Order
→ Fiscal Port
→ Bling ou provedor fiscal especializado
→ SEFAZ
```

Provedores candidatos devem ser comparados via ADR/POC; nomes mencionados na pesquisa incluem soluções como Nuvem Fiscal e TecnoSpeed/PlugNotas, sem decisão atual.

## Governo/SEFAZ possui integração oficial?

Sim, mas é importante usar a terminologia correta.

### NF-e/NFC-e

NF-e e NFC-e usam **WebServices oficiais das Secretarias de Fazenda**, com XML/XSD, certificado/assinatura, autorização, eventos e regras definidas pelo MOC e notas técnicas.

Não existe uma única chamada REST simples e estável do tipo `POST /gerar-nota` que cubra toda a fiscalidade nacional de mercadorias.

### NFS-e

O padrão nacional de **NFS-e** possui APIs e manuais oficiais do governo para emissão/integração, mas NFS-e trata **serviços**. Ela não substitui NF-e/NFC-e no fluxo comum de venda de roupas/mercadorias.

Ver documentação aprofundada em [Integração Fiscal no Brasil](../integrations/fiscal-brazil.md).

## São Paulo — contexto verificado em 2026

A Portaria SRE 79/2024 acrescentou regra vedando emissão de CF-e-SAT a partir de **1º de janeiro de 2026**. A página oficial da NFC-e da SEFAZ-SP informa que a NFC-e passou a ser obrigatória a partir dessa data para o varejo paulista em substituição aos documentos indicados pela Secretaria.

Portanto, qualquer projeto para varejo paulista precisa validar o fluxo atual de NFC-e/NF-e e contingência com SEFAZ/contador antes de produção.

Fontes oficiais:

- https://legislacao.fazenda.sp.gov.br/Paginas/Portaria-SRE-79-de-2024.aspx
- https://portal.fazenda.sp.gov.br/servicos/nfce/
- https://www.nfe.fazenda.gov.br/

Outras regras por tipo de destinatário/operação podem mudar; não congelar interpretação fiscal apenas neste documento.

## `FiscalProvider` conceitual

```text
issueDocument()
queryStatus()
cancelDocument()
downloadXml()
getPrintableRepresentation()
handleContingency()
getAuthorizationProtocol()
```

O domínio `sales` conhece resultado fiscal necessário, não detalhes específicos do SDK/provedor ou SOAP/XML da SEFAZ.

## Documento fiscal

Persistir referências suficientes para auditoria:

- tipo;
- environment;
- legal entity;
- chave;
- número/série;
- status;
- protocolo;
- provider;
- XML/representação segura;
- sale/order relation;
- timestamps;
- eventos de cancelamento/contingência.

## Idempotência fiscal

Repetir request não pode emitir dois documentos para a mesma operação.

Use chave de operação/documento e reconciliation com provider.

## Contingência

Antes de aposentar solução fiscal existente, definir e testar:

- SEFAZ indisponível;
- internet indisponível;
- provider indisponível;
- timeout sem saber se autorizou;
- documento rejeitado;
- certificado inválido/expirado;
- fila de transmissão posterior.

“Retry” sem consulta de status pode duplicar efeito ou piorar estado ambíguo.

---

# 2. Marketplace e fiscal

Canais podem impor etapas próprias.

A documentação atual do TikTok Shop para Brasil contém fluxo de upload de invoice/documento antes do fulfillment em cenários aplicáveis. Isso reforça a necessidade de integrar OMS + Fiscal Adapter + Channel Adapter.

Fluxo conceitual:

```text
Order TikTok/ML/Shopee/Site/WhatsApp
→ validar necessidade fiscal
→ emitir documento
→ autorização
→ upload/registro no canal quando exigido
→ fulfillment
```

Revalidar versão/scopes no onboarding real.

---

# 3. Pagamentos do varejo

Separar `Payment` da venda.

Uma venda pode ter:

```text
Dinheiro  R$ 50
Pix       R$ 100
```

ou outras combinações permitidas.

## Meios possíveis

- dinheiro;
- Pix;
- débito;
- crédito;
- parcelamento;
- voucher/vale quando negócio usar;
- link de pagamento;
- pagamento no WhatsApp quando elegível;
- outros provedores.

A lista final vem do Discovery.

## `Payment`

```text
id
organizationId
saleId/orderId
method
amount
status
provider?
providerReference?
idempotencyKey?
authorizedAt?
settledAt?
```

Estados dependem do método:

```text
PENDING → AUTHORIZED → CAPTURED/SETTLED
PENDING/AUTHORIZED → FAILED/CANCELLED
CAPTURED → REFUNDED/PARTIALLY_REFUNDED
```

Não aplicar state machine universal a dinheiro físico sem adaptação.

---

# 4. TEF, maquininha e Pix

Discovery precisa levantar:

- adquirentes;
- terminais;
- Smart POS;
- TEF;
- Pix integrado/manual;
- conciliação;
- parcelamento;
- taxas;
- comprovantes.

## Princípio de escopo PCI

Preferir que provedor certificado manipule dados sensíveis de cartão. Mora Core guarda referências/tokens permitidos, não PAN/CVV completo.

Qualquer expansão de escopo de cartão exige revisão de segurança/compliance.

## Integração

```text
POS / Site / WhatsApp
→ Payment Port
→ TEF/PSP/Provider
→ resultado
→ Order/Sale finalization/reconciliation
```

Timeout ambíguo exige consulta/reconciliation antes de cobrar novamente.

---

# 5. Pix e comprovantes

Possíveis cenários:

- Pix manual com confirmação humana;
- QR dinâmico via provider;
- webhook de confirmação;
- conciliação posterior;
- experiência de pagamento no WhatsApp quando disponível/elegível.

Webhooks são deduplicados e autenticados.

### Regra dura

**Screenshot, PDF ou imagem de comprovante isolado não confirma pagamento automaticamente.**

Preferir:

```text
webhook assinado do PSP
ou
consulta idempotente ao provider
```

Se não houver integração, usar:

```text
AWAITING_MANUAL_REVIEW
```

com aprovação humana auditada.

---

# 6. Caixa e conciliação

Pagamento e caixa são relacionados, mas distintos.

- dinheiro afeta caixa físico;
- Pix/cartão são reconciliados eletronicamente;
- taxas/settlement podem ocorrer depois.

Ver [Vendas, Caixa e Comissões](sales-cash-commissions.md).

---

# 7. Refund/devolução

Devolução precisa coordenar:

- regra comercial;
- estoque;
- payment refund;
- caixa;
- fiscal;
- comissão;
- marketplace quando aplicável.

Não implementar `delete sale`.

## Reembolso idempotente

Retry não pode devolver dinheiro duas vezes.

---

# 8. Billing SaaS não é Retail Payment

São contextos separados:

```text
Retail Payment
cliente final → lojista

SaaS Billing
lojista → Mora Core
```

Compartilhar nome `Payment` entre ambos sem boundary pode gerar erros graves. Ver [Billing SaaS](../saas/billing-entitlements.md).

---

# 9. Certificados e secrets

Certificado A1, CSC, tokens e credenciais:

- nunca no Git;
- nunca no frontend/mobile;
- storage/secret vault apropriado;
- criptografia;
- acesso mínimo;
- rotação/expiração monitorada;
- audit de uso administrativo;
- backup/restauração definidos.

---

# 10. DANFE, XML e DARF

### XML

É o documento eletrônico autorizado conforme o modelo e deve ter retenção/integridade adequadas.

### DANFE/DANFE-NFC-e

Representação auxiliar conforme especificações aplicáveis. Pode ser disponibilizada ao cliente por link/arquivo/canal quando apropriado.

### DARF

DARF é documento de arrecadação de tributos federais e **não é o documento normalmente gerado para entregar ao consumidor em cada venda de roupa**.

Evitar misturar obrigação tributária da empresa com documento fiscal da venda ao cliente.

---

# 11. Observabilidade

Métricas/alertas futuros:

- emissão com sucesso/rejeição;
- latência provider/SEFAZ;
- documentos em contingência;
- certificado perto de expirar;
- pagamento pendente/ambíguo;
- refund failure;
- reconciliation mismatch;
- provider outage;
- tempo `payment confirmed → fiscal authorized`.

Logs não contêm cartão/secret/XML inteiro indiscriminadamente.

---

# 12. Testes essenciais

## Fiscal

- autorização;
- rejeição;
- timeout ambíguo;
- retry idempotente;
- cancelamento;
- contingência;
- certificado inválido;
- provider indisponível;
- marketplace invoice workflow;
- legal entity/tenant isolation.

## Pagamento

- split payment;
- confirmação duplicada;
- timeout;
- refund parcial/total;
- caixa correto;
- transação concorrente;
- webhook duplicado/out-of-order;
- comprovante falso/manual;
- tenant isolation.

Testar adapters com sandbox/fixtures oficiais e poucos E2E contra ambiente real de homologação quando disponível.

---

# 13. Go-live gate fiscal

Antes de qualquer venda depender do Mora Core como autoridade fiscal:

- contador valida operação;
- CNPJ/topologia/LegalEntity conhecidos;
- certificado/CSC configurados;
- homologação passou;
- contingência documentada/testada;
- runbook;
- monitoring;
- backup de XML/documentos;
- cancelamento/rejeição testados;
- fallback definido;
- suporte sabe agir.

---

# Questões abertas

- quais CNPJs e regimes?
- NFC-e/NF-e por cenário?
- existe prestação de serviço que exija NFS-e?
- provider fiscal?
- certificado A1?
- adquirentes/TEF?
- Pix integrado?
- payment provider para site/WhatsApp?
- settlement/conciliação?
- marketplace fiscal flow inicial?
- contador receberá export/API?

## Relacionados

- [Integração Fiscal no Brasil](../integrations/fiscal-brazil.md)
- [Discovery](../discovery/operational-discovery.md)
- [Vendas/caixa](sales-cash-commissions.md)
- [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md)
- [Marketplaces](../integrations/marketplaces.md)
- [Segurança](../security/security-architecture.md)
