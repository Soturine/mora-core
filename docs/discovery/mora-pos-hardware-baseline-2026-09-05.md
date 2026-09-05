# Baseline de Campo — PDV, Hardware e Operação das Lojas Mora

> **Data da coleta:** 05/09/2026  
> **Status:** evidência de campo parcial, baseada em fotografias e telas reais das duas lojas.  
> **Objetivo:** substituir suposições genéricas por um inventário inicial verificável para orientar Discovery, migração, POS, segurança, compatibilidade de periféricos e plano de substituição dos sistemas legados.

Este documento registra somente o que foi possível observar com confiança razoável. Itens não confirmados permanecem explicitamente como `não validado`.

Não foram incluídos nomes de funcionários, CPF/CNPJ, valores de vendas, dados de clientes ou outros dados pessoais visíveis nas telas.

---

## 1. Resumo executivo

As duas lojas Mora **não usam hoje exatamente o mesmo stack de PDV/hardware**.

| Área | Mora feminina/familiar | Mora masculina |
| --- | --- | --- |
| PDV observado | **NOOVA Tecnologia** | **Lips Control** |
| Versão exibida | `4.01.00.134` | `4.01.00.134` |
| Caixa observado | `001` | `002` |
| Sistema operacional observado | Windows 11, 64 bits | Windows 10, 64 bits |
| Processador | Intel Core i3-3240 @ 3.40 GHz | Intel Celeron N3350 @ 1.10 GHz |
| RAM | 6 GB DDR3 | 6 GB |
| Computador/chassi | Dell OptiPlex 3010 indicado no sistema | mini-PC/chassi MLLSE observado; modelo exato não confirmado |
| Monitor | LG Flatron E1641 | LG Flatron W1943C |
| Leitor | YHD-8200, 1D/laser 650 nm | Knup KP-1026, leitor manual com fio |
| Impressora | Bematech MP-4200 TH | VERDE DYJ-5801 |
| Largura de papel observada | confirmar | 58 mm |
| Interface da impressora | confirmar no Windows/driver | USB, conforme etiqueta do equipamento |
| Gaveta | presente/associada à estação, modelo não confirmado | presente/associada à estação, modelo não confirmado |
| Terminal de pagamento | terminal físico observado; marca/modelo não confirmados | terminal físico observado; marca/modelo não confirmados |

### Consequência imediata

O Mora Core não pode partir da hipótese de “um único legado” ou “um único kit de hardware”. A migração precisa considerar **duas estações reais diferentes**, dois PDVs e periféricos heterogêneos.

---

## 2. Níveis de evidência

- **Confirmado visualmente:** nome/modelo/valor aparece diretamente em tela, etiqueta ou carcaça.
- **Inferido:** há forte indício, mas falta confirmação por configuração, driver, contrato ou teste funcional.
- **Não validado:** precisa de inspeção adicional antes de virar requisito ou decisão.

Nenhuma inferência deste documento deve ser transformada em contrato de software sem teste de campo.

---

# 3. Mora feminina/familiar

## 3.1 Estação

Confirmado visualmente:

- PDV **NOOVA Tecnologia**;
- versão exibida `4.01.00.134`;
- caixa `001` em telas observadas;
- monitor LG Flatron **E1641**;
- Windows 11, sistema de 64 bits;
- equipamento identificado pelo Windows como `SERVIDOR`;
- Dell **OptiPlex 3010** indicado na tela de sistema;
- Intel Core **i3-3240 @ 3.40 GHz**;
- **6 GB DDR3**;
- armazenamento mostrado pelo Windows em torno de **224 GB** no snapshot de campo.

A tela de configurações também mostrou Intel HD Graphics. O valor apresentado pelo Windows para memória gráfica não deve ser usado como requisito de GPU do Mora Core.

## 3.2 Leitor de código de barras

Confirmado visualmente:

- modelo **YHD-8200**;
- leitor manual 1D;
- indicação de diodo laser **650 nm**.

Ainda precisa ser validado:

- USB HID/keyboard wedge, serial virtual ou outro modo;
- VID/PID e driver real no Windows;
- sufixo configurado após leitura (`Enter`, `Tab` ou nenhum);
- comportamento de leitura repetida;
- simbologias habilitadas.

## 3.3 Impressora

Confirmado visualmente:

- **Bematech MP-4200 TH**;
- impressora térmica de recibo atualmente conectada à estação.

Ainda precisa ser validado no equipamento/Windows:

- porta/interface realmente usada;
- driver e versão;
- spooler vs comunicação direta;
- largura e corte configurados;
- gaveta conectada pela impressora ou por outro meio;
- comportamento ao ficar sem papel/offline.

## 3.4 Pagamento

Há uma maquininha/terminal físico amarelo na estação, porém **marca, modelo, adquirente e integração não foram confirmados**.

Não inferir que o terminal esteja integrado ao PDV apenas porque está no balcão.

---

# 4. Mora masculina

## 4.1 Estação

Confirmado visualmente:

- PDV **Lips Control**;
- versão exibida `4.01.00.134`;
- caixa `002` em tela observada;
- monitor LG Flatron **W1943C**;
- Windows 10, sistema de 64 bits;
- Intel Celeron **N3350 @ 1.10 GHz**;
- **6 GB RAM**.

Foi observado um mini-PC/chassi com marca **MLLSE** conectado à estação. A associação exata entre chassi e configuração do Windows é altamente provável, porém o modelo do mini-PC não foi confirmado e permanece `não validado`.

## 4.2 Leitor de código de barras

Confirmado visualmente:

- marca **Knup** na carcaça;
- etiqueta **KP-1026**;
- leitor manual com fio.

Ainda precisa ser validado:

- modo USB HID/keyboard wedge ou outro protocolo;
- VID/PID;
- configuração de prefixo/sufixo;
- simbologias;
- taxa de repetição e comportamento em foco errado.

## 4.3 Impressora

Confirmado pela etiqueta do equipamento:

- marca exibida na carcaça: **VERDE**;
- `Thermal Receipt Printer`;
- modelo **DYJ-5801**;
- `Paper Width: 58mm`;
- `Interface: USB`;
- `Cash Drawer: DC12V/1A`;
- `Power Input: DC12V/3A`.

Isso sugere capacidade de acionamento de gaveta pela impressora, mas a gaveta, pinagem e configuração reais ainda precisam de teste. A informação da etiqueta não prova que a função esteja sendo usada atualmente.

## 4.4 Pagamento

Há terminal de pagamento físico na estação. Marca/modelo/adquirente/TEF permanecem `não validados`.

---

# 5. Fluxos observados no NOOVA da loja feminina

As telas de campo confirmam que o legado atual já cobre mais do que uma tela simples de venda. Isso define **paridade operacional mínima a investigar**, não significa que o Mora Core precise copiar a UI ou a implementação atual.

## Venda e atendimento

Foram observados:

- seleção/atribuição de vendedor/operador;
- cliente padrão consumidor e seleção de cliente;
- busca de cliente por identificação, CPF/CNPJ e razão social;
- entrada por código digitado ou leitura do produto;
- consulta de últimas vendas;
- cancelamento da última venda ou seleção de outro cupom;
- desconto e acréscimo;
- condições de pagamento;
- finalização com múltiplas formas disponíveis na UI.

Formas exibidas no snapshot:

- dinheiro;
- crediário;
- crédito;
- débito;
- Pix.

`Crediário` foi observado como opção de UI, mas sua regra real, risco, cobrança, limite e integração ainda precisam de Discovery antes de entrar no domínio produtivo.

## Caixa

Foram observados:

- abertura/continuação de sessão;
- resumo da sessão;
- venda;
- sangria;
- suprimento;
- finalização de sessão;
- histórico/movimentos.

Isso reforça que o futuro Mora POS precisa ser validado contra o **fluxo completo de caixa**, não apenas contra “passar produto e imprimir”.

## Retaguarda

Na Central Administrativa foram observados atalhos para:

- cadastro de parceiros;
- NCM;
- cadastro de produtos;
- modificadores;
- impostos;
- empresa;
- formas de pagamento;
- retirada/consulta de documentos;
- problemas de integração;
- **Central de NFC-e**;
- etiquetas;
- central de vendas;
- promoções;
- gerenciamento de TEF;
- histórico de movimentos.

A presença de `Central de NFC-e` e `Gerenciamento do TEF` **não prova**, sozinha, quem é a autoridade fiscal ou de pagamento em toda a operação. Precisamos mapear a relação entre NOOVA, Lips, Bling, adquirentes e SEFAZ antes de definir ownership.

---

# 6. Catálogo e qualidade dos dados legados

A tela de produtos do NOOVA mostrou **5.968 registros encontrados** no snapshot de campo.

Foram observados padrões que exigem um pipeline de migração com staging e reconciliação:

- diversos itens classificados como `Sem Categoria`;
- saldos negativos em alguns registros;
- saldos fracionários muito pequenos, inclusive em itens com unidade `UN`;
- valores de compra zerados em alguns registros;
- nomenclatura histórica heterogênea.

## Regra de migração

Esses valores são **evidência do legado**, não modelo desejado do Mora Core.

Nunca fazer:

```text
legacy.quantity = -0.999
→ Core.availableQuantity = -1
```

sem política explícita e reconciliação.

O pipeline deverá preservar o valor bruto no staging/provenance, classificar anomalias e só materializar o saldo inicial após aprovação/reconciliação.

Exemplos de categorias de anomalia:

```text
NEGATIVE_BALANCE
FRACTIONAL_UNIT_BALANCE
MISSING_CATEGORY
ZERO_OR_UNKNOWN_COST
DUPLICATE_OR_AMBIGUOUS_IDENTIFIER
UNKNOWN_VARIANT_STRUCTURE
```

Não arredondar nem “corrigir” silenciosamente.

---

# 7. Implicações para a arquitetura do Mora POS

## 7.1 O POS não deve depender de fabricante

O domínio não conhece `Bematech`, `VERDE`, `YHD`, `Knup` ou qualquer marca.

Ele conhece capacidades:

```text
barcode_input
receipt_print
label_print
cash_drawer
payment_terminal
```

Adaptadores resolvem o equipamento concreto.

## 7.2 Scanner

A direção preferida é suportar, quando o equipamento estiver configurado assim, **keyboard-wedge/HID** como caminho simples e robusto:

```text
scanner
→ caracteres como teclado
→ buffer de leitura
→ normalização
→ lookup
```

Mas isso deve ser confirmado nos dois leitores atuais. Entrada manual sempre permanece como fallback.

O scan nunca autoriza sozinho uma operação crítica; permissões e regras ficam no backend/application core.

## 7.3 Impressora e gaveta

Para impressão crítica, não depender exclusivamente de APIs experimentais do navegador.

A arquitetura candidata é:

```text
Mora POS
→ API Mora Core para regras/transações
→ Local Device Bridge opcional
→ spooler/driver/porta local
→ impressora/gaveta
```

O bridge precisa ser estreito e seguro, por exemplo com operações explícitas:

```text
printReceipt
printLabel
openCashDrawer
getDeviceHealth
```

Ele **não** recebe SQL, token de banco, regra de estoque, regra fiscal ou comandos arbitrários.

## 7.4 WebUSB/WebSerial

MDN classifica WebUSB e Web Serial como recursos de **disponibilidade limitada**, fora do Baseline universal e dependentes de contexto seguro/permissões. Portanto, podem ser avaliados como melhoria progressiva, mas não devem ser a única base de impressão/serial do POS crítico.

Fontes:

- https://developer.mozilla.org/en-US/docs/Web/API/WebUSB_API
- https://developer.mozilla.org/en-US/docs/Web/API/Web_Serial_API

## 7.5 Web/PWA, desktop wrapper ou bridge local

A decisão final ainda exige ADR/POC.

Candidatos:

1. web/PWA + Local Device Bridge;
2. wrapper desktop leve;
3. aplicação desktop dedicada;
4. híbrido por capability.

Não escolher Electron, Tauri ou outro runtime apenas por moda. O piloto precisa provar:

- latência;
- memória;
- startup;
- compatibilidade de drivers;
- impressão;
- scanner;
- atualização;
- suporte remoto;
- offline/contingência.

O Celeron N3350 com 6 GB da loja masculina é uma **máquina de referência para o período de migração/shadow**, não um compromisso permanente de suporte por muitos anos.

---

# 8. Segurança dos endpoints da loja

## Windows 10

A Microsoft encerrou o suporte geral do Windows 10 em **14/10/2025**. O equipamento da loja masculina observado em Windows 10 precisa ter edição/build/lifecycle verificados: pode haver exceções como LTSC/ESU, mas isso não deve ser presumido.

Fonte oficial:

- https://support.microsoft.com/pt-br/windows/windows-10-suporte-terminou-a-14-de-outubro-de-2025-2ca8b313-1946-43d3-b55c-2b95b107f281

## Windows 11 no i3-3240

O equipamento feminino mostra Windows 11 em um Core i3-3240. As listas oficiais de processadores suportados pelo Windows 11 apresentam famílias Core significativamente mais novas, e os requisitos incluem processador compatível, UEFI/Secure Boot e TPM 2.0.

Não declarar a instalação como suportada ou não suportada somente pela foto. Fazer uma verificação real de:

- edição e build;
- Windows Update;
- TPM;
- Secure Boot/UEFI;
- elegibilidade oficial;
- drivers.

Fontes oficiais:

- https://www.microsoft.com/pt-br/windows/windows-11-specifications
- https://learn.microsoft.com/pt-br/windows-hardware/design/minimum/supported/windows-11-24h2-supported-intel-processors

## Remote support e software local

Foi observado software de suporte remoto e utilitários de terceiros em estações. O Mora Core precisa tratar isso como parte do endpoint inventory:

- quem pode acessar remotamente;
- MFA/controle de acesso;
- registro de sessão de suporte;
- contas locais/admin;
- atualização;
- antivírus/EDR;
- software desnecessário;
- política de instalação;
- recuperação do equipamento.

Não expor banco/cloud credentials na estação.

---

# 9. Requisitos de compatibilidade derivados

O futuro POS deve ser validado, no mínimo, contra:

1. scan rápido de produto;
2. fallback por digitação/pesquisa;
3. seleção correta de seller/cashier conforme política;
4. cliente consumidor e cliente identificado;
5. múltiplos meios de pagamento;
6. desconto/acréscimo com autorização;
7. venda concluída idempotente;
8. impressão de comprovante sem duplicar venda em retry;
9. gaveta, quando configurada;
10. sangria e suprimento;
11. cancelamento/devolução com trilha;
12. fechamento/reconciliação;
13. contingência de internet;
14. periférico desconectado/sem papel;
15. reinício da estação;
16. operação no hardware real das duas lojas durante piloto/shadow.

---

# 10. Itens ainda pendentes de Discovery

## Sistemas e dados

- qual capability hoje pertence a NOOVA, Lips, Bling ou outro sistema;
- APIs/exportações disponíveis em NOOVA e Lips;
- banco/local cache dos legados;
- ownership de catálogo, estoque, venda e fiscal;
- estratégia atual de backup/restore;
- qualidade real dos identificadores/SKUs;
- como grade/variante é representada nos dois sistemas.

## Fiscal

- quem solicita/assina/transmite a NFC-e hoje em cada loja;
- CNPJ/LegalEntity por estação;
- certificado A1/CSC e onde ficam;
- contingência atual;
- relação entre PDV, Bling e SEFAZ;
- cancelamento, rejeição e recuperação.

## Pagamentos

- marca/modelo/adquirente dos terminais;
- TEF realmente integrado ou uso manual;
- Pix integrado ou manual;
- parcelamento/crediário;
- conciliação;
- taxas e settlement.

## Hardware/rede

- interfaces exatas dos leitores;
- drivers/portas das impressoras;
- modelo e ligação da gaveta;
- impressora de etiquetas, se separada;
- rede/Wi-Fi;
- link principal e backup 4G/5G;
- nobreak;
- política de substituição dos PCs;
- impressora/scanner reserva.

---

# 11. Plano de validação em campo

Antes de desenvolver o POS definitivo:

```text
inventário de Device Manager/portas/drivers
→ export de configuração dos legados quando permitido
→ testar scan como teclado
→ imprimir página/teste e recibo de homologação
→ testar desconectar/reconectar
→ verificar gaveta
→ mapear TEF/Pix
→ observar abertura-venda-cancelamento-fechamento reais
→ mapear fiscal e contingência
→ medir tempo/latência/memória
→ documentar as-is
→ ADR do cliente POS/periféricos
```

A validação deve ocorrer nas duas lojas.

---

# 12. Fontes externas complementares

As fontes abaixo são snapshots de pesquisa e devem ser revalidadas antes da implementação:

- NOOVA PDV: https://www.noovatecnologia.com.br/
- Microsoft — fim do suporte do Windows 10: https://support.microsoft.com/pt-br/windows/windows-10-suporte-terminou-a-14-de-outubro-de-2025-2ca8b313-1946-43d3-b55c-2b95b107f281
- Microsoft — requisitos do Windows 11: https://www.microsoft.com/pt-br/windows/windows-11-specifications
- Microsoft Learn — processadores Intel suportados: https://learn.microsoft.com/pt-br/windows-hardware/design/minimum/supported/windows-11-24h2-supported-intel-processors
- MDN — WebUSB: https://developer.mozilla.org/en-US/docs/Web/API/WebUSB_API
- MDN — Web Serial: https://developer.mozilla.org/en-US/docs/Web/API/Web_Serial_API

A página pública da NOOVA consultada em 05/09/2026 declara, entre outras capacidades, Windows/Android/Smart POS, operação híbrida online + contingência, TEF/Pix e integração com equipamentos/rede. Isso é referência externa; o que está realmente contratado/configurado na Mora deve ser confirmado em campo.

---

## Relacionados

- [Discovery Operacional](operational-discovery.md)
- [Arquitetura](../architecture/system-architecture.md)
- [Fluxos end-to-end](../architecture/end-to-end-flows.md)
- [Vendas, caixa e comissões](../domain/sales-cash-commissions.md)
- [Fiscal e pagamentos](../domain/fiscal-and-payments.md)
- [Barcodes e scanning](../mobile/barcodes-and-scanning.md)
- [Migração](../data/import-export-migration.md)
- [Segurança](../security/security-architecture.md)
- [Roadmap](../roadmap/roadmap.md)
