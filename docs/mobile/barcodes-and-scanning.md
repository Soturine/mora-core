# Códigos de Barras, SKUs, Etiquetas e Scanner

> **Status:** planejado.

## Princípio

A identidade canônica de estoque é `variantId`. Códigos de barras, QR e RFID são identificadores associados, não a identidade do domínio.

## Tipos de identificador

- GTIN/EAN/UPC do fabricante, quando existir;
- SKU interno Mora Core;
- Code 128 ou simbologia adequada para etiqueta interna;
- QR interno para navegação/ações;
- RFID futuro, somente quando justificado.

## Produto sem código

Não inventar GTIN/EAN oficial. Para uso interno, gerar SKU único e barcode interno.

Exemplo:

```text
Produto: Vestido Helena
SKU base: VES-HEL-0001
Variantes:
VES-HEL-AZU-P
VES-HEL-AZU-M
VES-HEL-PRE-P
```

A geração precisa ser determinística o suficiente para evitar colisão, mas o identificador humano não deve carregar lógica de negócio excessiva. A unicidade real é protegida no banco.

## Etiquetas

Uma etiqueta pode conter:

- marca/loja;
- nome resumido;
- cor/tamanho;
- preço quando política exigir;
- código 1D;
- SKU legível;
- QR opcional.

Layouts e impressoras devem ser tratados por adapter/template. Não acoplar domínio a um fabricante de impressora.

## Scanner

```text
captura → decode → normalize → lookup → action
```

Regras:

- limitar frequência/eventos duplicados;
- feedback sonoro/háptico configurável;
- impedir +2 por leitura repetida involuntária;
- permitir entrada manual como fallback;
- validar check digit quando formato exigir;
- barcode desconhecido pode iniciar cadastro;
- scan nunca autoriza ação sozinho: permissões continuam server-side.

## Inventário em modo coletor

Cada scan incrementa a contagem local da variante. O resultado é uma contagem, não atualização direta de saldo. Divergências precisam ser revisadas e convertidas em movimento de ajuste.

## Código interno e canais externos

Marketplaces podem exigir GTIN em categorias específicas ou aceitar exceções. O Mora Core mantém separação entre:

```text
internalIdentifier
manufacturerIdentifier
channelIdentifier
```

O Compliance Engine por canal decide requisitos de publicação.

## QR interno

Pode codificar identificador opaco/URL segura para abrir a variante no app. Não incluir dados sensíveis ou autorização no QR.

## RFID

Deferido. O modelo de identificadores deve aceitar RFID no futuro sem remodelar `ProductVariant` ou `InventoryMovement`.

## Segurança

- não confiar no conteúdo lido pela câmera;
- validar tamanho/formato;
- QR/URLs tratados como input não confiável;
- não executar ação destrutiva automaticamente após scan;
- auditoria para ajuste/transferência/recebimento.

## Testes

- duplicidade de SKU/barcode;
- scan repetido rápido;
- barcode inválido;
- desconhecido;
- conflito offline;
- etiqueta correta para variante;
- autorização por loja/tenant;
- geração interna sem colisão.

## Relacionados

- [Aplicativo mobile](mobile-app.md)
- [Estoque](../domain/inventory.md)
- [Catálogo](../commerce/catalog-taxonomy.md)
- [Marketplaces](../integrations/marketplaces.md)
