# Integração Fiscal no Brasil — NF-e, NFC-e e NFS-e

> **Status:** pesquisa e arquitetura de integração. Não é parecer fiscal/contábil e não substitui documentação oficial vigente, contador ou homologação com provedor/SEFAZ.

## 1. Resposta curta: “o governo tem API para gerar nota?”

**Sim, existem interfaces e WebServices oficiais para documentos fiscais eletrônicos, mas não existe uma única API nacional simples que “gera qualquer nota” para qualquer operação.**

O cenário é dividido, entre outros, por tipo de documento:

- **NF-e (modelo 55):** mercadorias/operações aplicáveis;
- **NFC-e (modelo 65):** consumidor final/varejo conforme regras estaduais;
- **NFS-e:** serviços, com padrão nacional e APIs/documentação próprias.

NF-e/NFC-e foram desenhadas para integração dos sistemas das empresas com as Secretarias de Fazenda por WebServices, schemas XML, assinatura digital, regras de validação, autorização, eventos e contingência. O MOC — Manual de Orientação do Contribuinte — define os critérios técnicos de integração.

A NFS-e Padrão Nacional possui documentação e APIs próprias para contribuintes/municípios, mas **NFS-e é nota de serviço; não é substituta de NF-e/NFC-e para venda de mercadorias**.

---

## 2. Fontes oficiais verificadas em 26/08/2026

### NF-e/NFC-e

Portal Nacional NF-e:

- https://www.nfe.fazenda.gov.br/
- MOC 7.0 e anexos;
- notas técnicas vigentes;
- schemas e regras de validação.

O portal registra notas técnicas vigentes em 2026, incluindo adaptações de leiaute para Reforma Tributária e CNPJ alfanumérico. Portanto, qualquer implementação deve ser version-aware.

### NFC-e São Paulo

SEFAZ-SP:

- https://portal.fazenda.sp.gov.br/servicos/nfce/

Em SP, a emissão de NFC-e passou a ser obrigatória em 01/01/2026 para o varejo paulista em substituição ao CF-e-SAT e modelos indicados pela SEFAZ, sem eliminar a necessidade de avaliar quando a operação exige NF-e.

A SEFAZ-SP também possui WebServices de apoio à escrituração para consulta de chaves e download de XML, com autenticação por certificado digital do contribuinte.

### NFS-e padrão nacional

Portal NFS-e:

- https://www.gov.br/nfse/
- https://www.gov.br/nfse/pt-br/biblioteca/documentacao-tecnica/documentacao-atual

A documentação atual inclui, entre outros:

- API Manual de Contribuintes — Emissor Público;
- guias de utilização das APIs;
- layouts;
- schemas XSD.

Novamente: aplicável a **serviços**, não ao fluxo principal de venda de roupa/mercadoria.

---

## 3. Opções arquiteturais para o Mora Core

### Opção A — usar Bling como autoridade fiscal inicialmente

```text
Mora Core Order/Sale
→ Bling Adapter
→ emissão fiscal
→ SEFAZ
→ resultado/XML/chave
→ Mora Core registra referência/status
```

É a direção de menor risco para início, porque a operação já utiliza um ERP existente e fiscalidade não deve ser o primeiro subsistema substituído.

### Opção B — provedor fiscal especializado

```text
Mora Core
→ FiscalPort
→ Provider especializado
→ SEFAZ
```

Vantagens possíveis:

- abstração de certificados;
- schemas/NTs;
- contingência;
- webhooks/status;
- redução do custo de manter protocolo fiscal diretamente.

Provedores candidatos só serão escolhidos por ADR/POC e validação comercial/técnica.

### Opção C — integração direta com WebServices oficiais

Tecnicamente possível em cenários aplicáveis, mas significa assumir:

- XML/XSD;
- assinatura digital;
- certificado;
- CSC/QR Code quando aplicável;
- endpoint por UF/ambiente;
- autorização;
- rejeições;
- eventos;
- cancelamento;
- inutilização quando aplicável;
- contingência;
- notas técnicas;
- alterações tributárias;
- armazenamento de XML;
- monitoramento;
- suporte operacional.

Não é recomendação inicial.

---

## 4. `FiscalPort`

Contrato conceitual independente de provider:

```text
FiscalPort
├── issueDocument(command)
├── queryDocumentStatus(reference)
├── cancelDocument(command)
├── getDocument(reference)
├── getXml(reference)
├── getPrintableRepresentation(reference)
└── health/capabilities
```

O domínio não deve conhecer SOAP/XML específico da SEFAZ ou SDK do Bling.

---

## 5. `FiscalDocument`

```text
FiscalDocument
├── id
├── organizationId
├── legalEntityId
├── orderId?/saleId
├── type                 // NFE | NFCE | NFSE | ...
├── environment          // HOMOLOGATION | PRODUCTION
├── provider
├── providerReference?
├── accessKey?
├── number?
├── series?
├── status
├── authorizationProtocol?
├── issuedAt?
├── authorizedAt?
├── cancelledAt?
├── xmlAssetRef?
├── printableAssetRef?
├── failureCode?
└── timestamps
```

Não guardar segredo/certificado dentro do documento.

---

## 6. State machine

Exemplo genérico:

```text
PENDING
→ SUBMITTED
→ AUTHORIZED

SUBMITTED
→ REJECTED
→ UNKNOWN_RECONCILIATION_REQUIRED

AUTHORIZED
→ CANCELLATION_REQUESTED
→ CANCELLED
```

Estados reais variam por documento/provedor.

`UNKNOWN_RECONCILIATION_REQUIRED` é importante: timeout não significa falha. Reenviar cegamente pode gerar efeitos indesejados.

---

## 7. Fiscal na venda via WhatsApp/site/marketplace

Fluxo desejado:

```text
Cart
→ Order
→ Payment = PAID/condição aplicável
→ Sale/Order state apropriado
→ Fiscal job
→ FiscalPort
→ AUTHORIZED
→ disponibilizar documento
→ fulfillment
```

Alguns marketplaces podem exigir documento antes de etapas específicas de fulfillment. O `ChannelAdapter` coordena isso sem contaminar `Fiscal` com lógica de marketplace.

---

## 8. DANFE e XML

O documento fiscal eletrônico é o XML autorizado conforme o modelo. DANFE/DANFE-NFC-e é representação auxiliar conforme especificações aplicáveis.

O Mora Core deve armazenar/reter os documentos conforme obrigação e política, com:

- integridade/checksum;
- acesso restrito;
- backup;
- export para contabilidade;
- lifecycle definido.

O cliente pode receber uma representação/arquivo/link permitido pelo canal.

---

## 9. DARF

**DARF não é o comprovante fiscal normal enviado ao consumidor após uma venda de roupa.**

DARF é Documento de Arrecadação de Receitas Federais, ligado ao recolhimento de tributos federais em situações específicas.

Portanto, no fluxo comercial comum do Mora Core, o foco é NF-e/NFC-e e, se a empresa prestar serviços, NFS-e conforme aplicabilidade.

---

## 10. CNPJ/LegalEntity

Fiscal pertence à entidade legal correta.

```text
Organization
├── LegalEntity A
│   ├── certificado
│   ├── configuração fiscal
│   └── séries/ambientes
└── LegalEntity B
    └── ...
```

Nunca emitir usando “CNPJ da organização” como string global sem modelagem.

---

## 11. Certificado digital e secrets

Certificado A1, senha, CSC/tokens e credenciais:

- Secret Manager/KMS;
- criptografia em repouso;
- access policy mínima;
- nunca browser/mobile;
- nunca Git;
- nunca log;
- rotação/renovação;
- alerta antes do vencimento;
- audit de acesso administrativo.

Se provider gerenciado armazenar certificado, avaliar contrato, segurança e exportabilidade.

---

## 12. Homologação e produção

Separação forte:

```text
HOMOLOGATION
PRODUCTION
```

- credenciais/certificados/contextos separados;
- dados de teste marcados;
- nenhum E2E de CI deve emitir produção por acidente;
- permissões de produção mais restritas.

---

## 13. Rejeições

Não mostrar ao operador apenas:

> Erro 539.

Criar catálogo de erros por provider/documento com:

- código original;
- mensagem original;
- explicação operacional;
- retryable?;
- ação sugerida;
- precisa contador/suporte?;
- correlation ID.

Nunca “corrigir” automaticamente dado tributário usando IA.

---

## 14. Contingência

Planejar/testar:

- internet da loja indisponível;
- Mora Core indisponível;
- provider indisponível;
- SEFAZ indisponível;
- certificado expirado;
- status ambíguo;
- fila represada;
- rejeição após pagamento.

Contingência fiscal é específica do documento/UF/regra vigente; não inventar procedimento genérico.

---

## 15. Reconciliation

Jobs verificam:

```text
Mora Core FiscalDocument
vs
provider/SEFAZ state
```

Detectar:

- autorizado externamente mas Core pendente;
- cancelamento divergente;
- XML ausente;
- documento duplicado;
- documento sem sale/order associável.

---

## 16. Integração com contador

Capacidades futuras:

- export XML por período/legal entity;
- relatórios fiscais;
- acesso `ACCOUNTANT` restrito;
- pacote/integração contábil;
- auditoria de download.

Não enviar XML contendo dados de outro tenant/legal entity.

---

## 17. Observabilidade

Métricas:

- authorization success rate;
- rejection rate por código;
- provider latency;
- queue age;
- contingência ativa;
- certificate expiry days;
- reconciliation mismatches;
- tempo payment → fiscal authorized.

Alertas acionáveis, não alarmes por cada rejeição de cadastro corrigível.

---

## 18. Testes

### Contract/fixture

- XML/schema fixtures oficiais;
- provider sandbox;
- códigos de rejeição;
- assinatura/certificado de homologação.

### Integração

- autorização;
- consulta;
- cancelamento;
- timeout ambíguo;
- retry idempotente;
- provider outage;
- certificado inválido;
- tenant/legal entity isolation.

### Recovery

- reiniciar worker durante emissão;
- recuperar estado após timeout;
- reconciliar documento autorizado externamente.

---

## 19. Go-live gate

Antes de tornar o Mora Core autoridade fiscal:

- contador valida cenários;
- legal entities/CNPJs mapeados;
- documento correto por operação;
- provider/integração homologado;
- certificado e secrets seguros;
- homologação executada;
- contingência testada;
- reconciliation testada;
- XML/backup/export testados;
- runbook;
- observabilidade;
- fallback definido;
- rollback operacional possível.

---

## 20. Decisão recomendada atual

```text
Fase inicial
Mora Core → Bling/fiscal existente

Depois
Mora Core → FiscalPort → provider especializado

Integração direta SEFAZ
somente se benefício real justificar o custo/risco
```

## Relacionados

- [Fiscal e pagamentos](../domain/fiscal-and-payments.md)
- [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Bling](bling.md)
- [Segurança](../security/security-architecture.md)
- [Incidentes](../operations/incident-recovery.md)
