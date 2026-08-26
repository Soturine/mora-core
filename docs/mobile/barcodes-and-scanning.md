# Códigos de Barras, Etiquetas e Scanner

## Identificadores

Separar `variantId`, `SKU`, `GTIN/EAN`, barcode interno, QR e futuramente RFID. Nenhum deles substitui a identidade interna da variante.

## Produtos sem código

Gerar SKU/código interno único no escopo da organização. Para barcode interno, Code 128 é uma opção compatível com leitores comuns. Não inventar EAN/GTIN oficial: uso comercial padronizado deve seguir GS1 quando necessário.

## Fluxo scanner

Câmera → detectar código → buscar variante → se existe, abrir consulta/ação; se desconhecido, oferecer cadastro; se não há código, gerar identificador interno.

## Etiqueta

Pode conter marca, nome resumido, cor/tamanho, SKU/barcode e preço quando aplicável. Template por organização/loja/impressora.

## Inventário

Modo scanner conta rapidamente e acumula operações. Duplicidade, debounce e leitura repetida devem ser tratados deliberadamente.

## Segurança/qualidade

Não confiar na leitura como autorização. Scanner identifica; servidor valida operação, tenant, estoque e permissão.
