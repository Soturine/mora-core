# Fiscal e Pagamentos

## Princípio

Fiscal é domínio de alto risco regulatório e deve ficar atrás de um `FiscalProvider`/adapter. O Mora Core não deve começar implementando protocolo fiscal brasileiro completo do zero.

## São Paulo

A operação real deve ser validada continuamente com contador e documentação oficial vigente. Regras fiscais mudam. O projeto já identificou a migração paulista para NFC-e e necessidade de NF-e em cenários específicos; nenhuma regra deve ser congelada sem revalidação antes de produção.

## Direção

Fase inicial: manter provedor/ERP existente como autoridade fiscal ou integrar um provedor especializado (ex.: Nuvem Fiscal/TecnoSpeed) após ADR e validação comercial/técnica.

## Interface conceitual

`issueDocument`, `cancelDocument`, `queryStatus`, `downloadXml`, `handleContingency` e eventos relacionados, sem vazar detalhes do provedor para `sales`.

## Pagamentos

Separar:
- pagamento do cliente da loja;
- conciliação/adquirente/TEF/Pix;
- billing da assinatura Mora Core.

Nunca armazenar dados completos de cartão se um provedor certificado puder assumir esse escopo.

## Idempotência

Criação/captura/fiscalização retryable deve usar chaves idempotentes e reconciliação. Repetir request nunca pode gerar cobrança ou documento duplicado.
