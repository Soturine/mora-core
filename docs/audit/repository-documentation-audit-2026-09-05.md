# Auditoria Documental e Operacional — 05/09/2026

> **Escopo:** revisar a documentação do Mora Core após o levantamento fotográfico dos PDVs e periféricos das duas lojas Mora e incorporar oportunidades arquiteturais, de migração, QA e segurança.  
> **Estado do repositório:** `documentation-first`; esta auditoria não afirma que o software esteja implementado.

---

## 1. Resultado executivo

A documentação anterior já estava forte em visão de produto, domínio, SaaS, WhatsApp, mobile, IA, fiscal, marketplace e princípios de engenharia. O maior gap encontrado após o levantamento de campo era **operação física concreta**:

- quais PDVs existem hoje;
- quais computadores/periféricos precisam sobreviver à migração;
- como tratar impressão/scanner/gaveta;
- como provar compatibilidade;
- como migrar milhares de dados legados potencialmente inconsistentes;
- como separar impressão, pagamento e fiscal;
- como evitar um cutover big-bang.

Esse gap foi reduzido com documentação canônica nova e atualização dos documentos dependentes.

---

## 2. Evidência nova incorporada

### Mora feminina/familiar

Observado em campo:

- NOOVA Tecnologia;
- caixa `001` e versão `4.01.00.134` no snapshot;
- Windows 11 64-bit;
- Dell OptiPlex 3010 / Core i3-3240 / 6 GB DDR3;
- LG Flatron E1641;
- leitor YHD-8200;
- Bematech MP-4200 TH.

Também foram observadas telas de:

- venda;
- seller/cliente;
- formas de pagamento;
- desconto/acréscimo;
- sangria;
- suprimento;
- fechamento/sessão;
- últimas vendas;
- cancelamento;
- produto;
- NCM/impostos;
- etiquetas;
- Central de NFC-e;
- gerenciamento de TEF;
- retaguarda administrativa.

### Mora masculina

Observado em campo:

- Lips Control;
- caixa `002` e versão `4.01.00.134` no snapshot;
- Windows 10 64-bit;
- Celeron N3350 / 6 GB;
- LG Flatron W1943C;
- leitor Knup KP-1026;
- impressora VERDE DYJ-5801 USB, papel 58 mm;
- mini-PC/chassi MLLSE observado, modelo exato ainda não confirmado.

Nenhum nome de funcionário, CPF/CNPJ, identificador de dispositivo ou valor de venda foi copiado para a documentação.

---

## 3. Achado crítico: dois legados, não um

A visão anterior tratava “ERP/PDV atual” de forma muito genérica e dava forte destaque à futura transição via Bling.

O campo mostrou que precisamos investigar pelo menos:

```text
loja feminina/familiar → NOOVA
loja masculina         → Lips Control
Bling                   → papel atual ainda precisa ser mapeado
```

### Correção arquitetural

Antes de integrar ou substituir qualquer sistema, definir ownership por capability:

```text
Catalog
Inventory
Sales
Cash
Payments
Fiscal
Customers
```

Uma integração com Bling não pode ser assumida como solução completa se o estado operacional autoritativo estiver no NOOVA/Lips em determinada capability.

---

## 4. Achado crítico: qualidade de dados

A tela de produtos do NOOVA mostrou **5.968 registros** e evidências visuais de:

- itens `Sem Categoria`;
- saldo negativo;
- saldo fracionário residual em unidade `UN`;
- custo zero/desconhecido em algumas linhas;
- nomenclatura histórica heterogênea.

### Oportunidade/correção

Foi documentado um pipeline específico de migração:

```text
raw snapshot
→ staging
→ anomaly detection
→ mapping/dedupe
→ dry-run
→ reconciliação
→ import canônico
```

O legado não define a policy futura. Saldo `-0.999`, por exemplo, não deve virar `-1` silenciosamente.

---

## 5. Achado crítico: hardware heterogêneo

Os dois caixas usam leitores e impressoras diferentes.

### Oportunidade/correção

O domínio passa a ser explicitamente orientado a capabilities:

```text
barcode_input
receipt_print
label_print
cash_drawer
payment_terminal
```

Fabricante/modelo fica em adapter/configuração da estação.

Isso protege o futuro SaaS contra uma arquitetura “funciona apenas na Bematech da Mora”.

---

## 6. Decisão arquitetural ainda aberta: cliente POS

Não foi congelado Electron/Tauri/PWA/desktop.

Candidatos documentados:

1. PWA/web + Local Device Bridge;
2. wrapper desktop leve;
3. cliente desktop dedicado;
4. híbrido por capability.

### Critério

A escolha será por POC/ADR medindo nas duas lojas:

- memória;
- CPU;
- startup;
- scan-to-item;
- impressão;
- reconnect;
- update/rollback;
- segurança;
- offline/fallback.

Isso está alinhado ao princípio de evitar overengineering e decisões por moda tecnológica.

---

## 7. Local Device Bridge

Foi documentada uma alternativa para periféricos que browser/PWA não acesse de forma confiável.

### Invariante

Se existir, o bridge é **estreito**:

```text
health
devices
printReceipt
printLabel
openCashDrawer
```

Não recebe:

- SQL;
- shell;
- token de banco;
- secrets de marketplace;
- filesystem arbitrário;
- regra de estoque/fiscal.

### Segurança

- bind local/IPC;
- allowlist/auth apropriada;
- schema validation;
- least privilege;
- update verificado/assinado quando aplicável;
- logs sanitizados.

---

## 8. WebUSB/Web Serial

Pesquisa em MDN mostrou que WebUSB e Web Serial têm disponibilidade limitada/não universal. Portanto, a documentação não os trata como fundação exclusiva de uma função crítica de caixa.

Podem ser usados como progressive enhancement/POC quando a matriz de navegador/dispositivo provar valor.

Fontes:

- https://developer.mozilla.org/en-US/docs/Web/API/WebUSB_API
- https://developer.mozilla.org/en-US/docs/Web/API/Web_Serial_API

---

## 9. Impressão != fiscal

O campo mostrou impressoras térmicas nos dois caixas e, no NOOVA, uma Central de NFC-e.

A documentação agora reforça:

```text
FiscalPort → autorização/documento
PrinterAdapter → saída física
```

Uma impressora térmica não se torna autoridade fiscal porque imprime o documento.

Reimpressão também não pode refazer venda/emissão.

---

## 10. Pagamentos e TEF

A UI do NOOVA exibe dinheiro, crediário, crédito, débito e Pix, e possui tela/atalho de gerenciamento de TEF. Terminais físicos estão presentes.

Isso **não é suficiente** para afirmar:

- qual adquirente;
- se TEF está realmente integrado;
- como Pix confirma;
- quem concilia;
- qual sistema é autoridade.

Esses itens foram mantidos abertos no Discovery/risks.

---

## 11. Fiscal

A presença de `Central de NFC-e` no NOOVA muda a pergunta de Discovery: não basta perguntar “usa Bling para nota?”. Precisamos observar a venda real e mapear:

```text
PDV
→ quem monta documento?
→ quem assina?
→ quem transmite?
→ onde fica certificado/CSC?
→ quem armazena XML?
→ quem reconcilia?
```

A estratégia final continua atrás de `FiscalPort` e não será inferida pela tela.

---

## 12. Endpoints e sistema operacional

A pesquisa oficial da Microsoft indica fim do suporte geral do Windows 10 em 14/10/2025, sujeito a exceções específicas como LTSC/ESU. Isso cria um risco operacional a verificar na estação masculina.

A estação feminina mostra Windows 11 em um Core i3-3240. A documentação passou a exigir verificação real de edição/build, Windows Update, TPM, Secure Boot/UEFI, elegibilidade e drivers, sem inferir suporte apenas por fotografia.

Fontes:

- https://support.microsoft.com/pt-br/windows/windows-10-suporte-terminou-a-14-de-outubro-de-2025-2ca8b313-1946-43d3-b55c-2b95b107f281
- https://www.microsoft.com/pt-br/windows/windows-11-specifications
- https://learn.microsoft.com/pt-br/windows-hardware/design/minimum/supported/windows-11-24h2-supported-intel-processors

---

## 13. QA que faltava

Foi criado plano específico de hardware real. O POS não será chamado de compatível apenas porque testes de browser passam.

Cenários incluem:

- scanner repetido/desconectado;
- entrada manual;
- impressora sem papel/offline;
- reimpressão;
- bridge indisponível;
- gaveta;
- pagamento/fiscal ambíguo;
- perda de internet;
- reinício no meio da venda;
- update/rollback;
- soak test de turno;
- consumo CPU/RAM;
- shadow contra legado.

---

## 14. Migração segura

A estratégia recomendada ficou mais precisa:

```text
extract/read-only
→ snapshot
→ staging/dry-run
→ Core projection
→ shadow
→ piloto
→ cutover por capability
→ fallback
```

Evitar big-bang simultâneo de:

- NOOVA/Lips;
- dados;
- periféricos;
- pagamentos;
- fiscal.

---

## 15. Oportunidades de produto SaaS identificadas

Sem transformá-las automaticamente em backlog, o campo sugere oportunidades reais:

### Migration Workbench

Ferramenta de importação, quality report, mapping e reconciliation para lojistas que já têm milhares de produtos.

### Device onboarding

Assistente para detectar/testar scanner/impressora/gaveta e registrar compatibilidade por estação.

### Health de loja

Painel operacional de:

- bridge/client version;
- printer health;
- scan failures;
- sync/backlog;
- Internet/fallback;
- payment/fiscal adapter health.

### POS rápido sem vendor lock-in

Experiência moderna que preserve atalhos/velocidade esperados por operador de caixa, mas com domínio central e adapters substituíveis.

### Data Quality Assistant

Relatório de produtos sem categoria, duplicados, saldo anômalo, SKU/GTIN inconsistente e grade incompleta antes de publicar/migrar.

Essas oportunidades só entram em implementação após problema, usuário, impacto e critério de aceite claros.

---

## 16. Documentos criados/atualizados neste passe

Criados:

- `docs/discovery/mora-pos-hardware-baseline-2026-09-05.md`;
- `docs/architecture/pos-device-integration.md`;
- `docs/data/mora-legacy-data-migration.md`;
- `docs/qa/pos-hardware-test-plan.md`;
- este relatório.

Atualizados:

- `docs/index.md`;
- `docs/architecture/system-architecture.md`;
- `docs/product/modules.md`;
- `docs/roadmap/risks-and-open-questions.md`;
- `docs/roadmap/documentation-coverage.md`;
- `docs/roadmap/roadmap.md`;
- `AGENTS.md`.

Outros documentos permanecem válidos e não foram alterados apenas para gerar churn.

---

## 17. O que ainda não está validado

- CNPJs/topologia legal;
- ownership NOOVA/Lips/Bling por capability;
- APIs/exportações dos legados;
- drivers e portas;
- conexão real das gavetas;
- adquirentes/terminais;
- TEF/Pix;
- fiscal end-to-end;
- regra de comissão;
- trocas/devoluções;
- rede/nobreak/backup Internet;
- estratégia definitiva do cliente POS;
- hardware mínimo final;
- política de renovação de estações;
- dump real do catálogo e contagens de anomalias.

Esses itens não foram inventados para “fechar” a documentação.

---

## 18. Próximo passo recomendado

Antes de escrever o primeiro Mora POS produtivo:

1. inventário de Device Manager, drivers e portas nas duas lojas;
2. observar uma venda real completa em cada legado;
3. mapear pagamento/TEF/Pix;
4. mapear fiscal/NFC-e e contingência;
5. obter export/API suportado de NOOVA/Lips/Bling;
6. rodar data profiling real;
7. definir ownership por capability;
8. realizar spike de scanner + impressão nos dois PCs;
9. ADR do cliente POS/Device Bridge;
10. só então iniciar o slice de Sales/POS com shadow.

Isso reduz o risco de construir uma interface bonita que não consegue substituir a operação do balcão.

---

## Relacionados

- [Índice](../index.md)
- [Baseline de campo](../discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [POS/periféricos](../architecture/pos-device-integration.md)
- [Migração dos legados](../data/mora-legacy-data-migration.md)
- [Plano de testes do POS](../qa/pos-hardware-test-plan.md)
- [Roadmap](../roadmap/roadmap.md)
- [Riscos](../roadmap/risks-and-open-questions.md)
