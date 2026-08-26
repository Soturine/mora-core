# Aplicativo Mobile

> **Status:** arquitetura e experiência planejadas. React Native + Expo + TypeScript é a preferência atual, sujeita a ADR antes da implementação.

## Papel do aplicativo

O Mora Mobile não é apenas o painel web reduzido para uma tela menor. Ele existe para explorar capacidades próprias do celular: câmera, mobilidade dentro da loja, leitura de códigos, fotografia de produtos, inventário, notificações e operação rápida em balcão/estoque.

O mesmo app deve funcionar para organizações diferentes no futuro SaaS. Após autenticação, a pessoa opera dentro do `OrganizationContext` e, quando necessário, de um `StoreContext`.

## Perfis e experiências

### Vendedor

Pode visualizar, conforme permissão:

- minhas vendas do dia/período;
- peças vendidas;
- comissão provisionada/estimada;
- metas quando configuradas;
- últimas vendas;
- consulta de produto/estoque;
- scanner;
- criação de venda quando o fluxo mobile for habilitado.

Não deve receber automaticamente acesso a margem, custo, vendas de outros funcionários ou dados gerenciais.

### Operador de estoque

- receber mercadoria;
- escanear produtos;
- criar rascunho de produto;
- registrar variantes;
- fotografar;
- consultar saldo por loja/local;
- inventariar;
- transferir estoque;
- solicitar ajuste.

### Gerente/proprietário

- visão consolidada de vendas;
- estoque baixo/zerado;
- produtos parados;
- caixas abertos/fechados;
- divergências;
- performance por loja/vendedor/canal;
- aprovação de ações sensíveis;
- publicação de catálogo;
- alertas operacionais.

## Fluxo: entrada de mercadoria

```text
Abrir Mora Mobile
→ selecionar loja/local de estoque
→ escanear GTIN/barcode ou pesquisar
→ produto existe?
    ├── sim: abrir variante/recebimento
    └── não: iniciar cadastro
→ informar dados mínimos
→ fotografar produto
→ criar/confirmar variantes
→ informar quantidades
→ validar
→ registrar recebimento
→ opcionalmente enviar para revisão/publicação
```

A entrada física de estoque e a publicação comercial são processos relacionados, mas independentes. Produto recebido não precisa aparecer instantaneamente no site sem revisão.

## Cadastro de produto

Campos dependem da categoria, mas o fluxo pode envolver:

- nome interno/canônico;
- marca;
- fornecedor;
- categoria/subcategoria;
- atributos estruturados;
- custo;
- preço/lista de preço;
- variantes;
- código externo ou SKU interno;
- quantidade recebida;
- loja/local de estoque;
- fotos;
- status de publicação.

IA pode sugerir nome, categoria, descrição e tags, mas a pessoa confirma antes de virar `Product Truth`.

## Cadastro rápido de variantes

Para moda, a UX precisa permitir matriz sem cadastro manual repetitivo.

Exemplo:

```text
Vestido Helena

Cores: Azul, Preto, Bege
Tamanhos: P, M, G, GG

          P   M   G   GG
Azul      ✓   ✓   ✓   —
Preto     ✓   ✓   —   —
Bege      —   ✓   ✓   ✓
```

Cada combinação válida gera `ProductVariant` com seu próprio `variantId`, SKU e, quando aplicável, código de barras, preço, mídia e estoque.

A interface não deve permitir combinação inexistente apenas porque cor e tamanho aparecem em duas listas independentes.

## Fotografia

A câmera deve facilitar múltiplas fotos por produto/variante:

```text
Produto
├── mídia geral
├── Azul
│   ├── frente
│   ├── costas
│   └── detalhe
└── Preto
    ├── frente
    └── detalhe
```

Original é enviado e preservado. Tratamentos e IA são derivados; veja [pipeline de mídia](../ai/media-pipeline.md).

## Scanner de código

A câmera pode reconhecer GTIN/EAN/UPC/Code 128/QR conforme bibliotecas e plataforma escolhidas.

Fluxo:

```text
scan
→ normalizar valor
→ buscar identificador
→ encontrou?
    ├── abrir variante
    └── oferecer cadastro
```

Se o item não possuir código externo, o Mora Core pode gerar identificador interno e etiqueta. Não inventar GTIN oficial. Veja [códigos de barras e etiquetas](barcodes-and-scanning.md).

## Inventário

Modo coletor deve priorizar velocidade:

```text
scan → +1
scan → +1
scan → +1
```

A UI mantém último item, contagem e erros claros. O resultado forma `InventoryCount`; divergências não sobrescrevem saldo automaticamente.

Fluxo recomendado:

```text
Contagem
→ comparar esperado x contado
→ revisar divergências
→ aprovação quando necessária
→ gerar InventoryAdjustment
```

## Transferências

```text
Selecionar origem
→ scan/selecionar variante
→ destino
→ quantidade
→ confirmar
```

Se a operação exigir cadeia de custódia:

```text
CREATED → SENT → RECEIVED
```

Para lojas próximas pode existir transferência imediata configurável, ainda assim auditada.

## Estoque baixo e alertas

Exemplos:

- estoque crítico;
- sem estoque;
- transferência aguardando recebimento;
- divergência de inventário;
- caixa com divergência;
- publicação com erro;
- integração/canal com falha.

Notificações devem ser acionáveis, deduplicadas e respeitar preferências/roles. Evitar spam.

## Caixa no celular

Pode existir para perfis autorizados.

Exemplo de fechamento:

```text
Dinheiro contado: R$ ____
Pix: reconciliado
Débito: reconciliado
Crédito: reconciliado
```

Fechamento cego e aprovação de divergência seguem o domínio de [vendas, caixa e comissões](../domain/sales-cash-commissions.md).

## Dashboard

### Proprietário/gerente

- faturamento hoje/período;
- comparação com período anterior apropriado;
- ticket médio;
- peças;
- mais vendidos;
- estoque baixo;
- aging/encalhados;
- vendas por loja/canal/vendedor;
- comissões;
- caixas e divergências.

### Vendedor

Somente métricas permitidas, tipicamente próprias.

## Offline

Offline é útil especialmente para inventário e rascunhos, mas é uma fonte de complexidade. Não prometer operação offline total sem especificação.

Estratégia conceitual:

```text
SQLite/local persistence
        +
local outbox
        ↓
operationId idempotente
        ↓
Mora Core API
```

Regras:

- cada operação sincronizável possui ID estável;
- retry não duplica movimento;
- conflito não é resolvido silenciosamente;
- operações financeiras/autorizações críticas podem exigir servidor online;
- dados locais sensíveis precisam de proteção e lifecycle;
- logout/revogação considera cache local.

### Rascunho offline

Produto/fotos podem existir como `DRAFT_LOCAL` e só se tornar registro canônico após sync, validação de SKU/unicidade e resposta do servidor.

## Upload resiliente

Não bloquear o operador esperando tratamento de imagem/IA.

```text
capturar
→ salvar metadata local segura
→ upload original
→ servidor confirma
→ criar job de derivados
→ app continua operação
→ status atualiza depois
```

Uploads grandes precisam de limites, timeout, retry controlado, resumable upload quando justificado e feedback de progresso.

## Arquitetura de IA

Nunca:

```text
App → chave do provedor de IA
```

Sempre:

```text
App → Mora Core → AI Gateway/adapter → provedor
```

Isso protege secrets, quotas, auditoria, modelos e possibilidade de trocar fornecedor.

## QR e RFID

O estoque depende de `variantId`, não da tecnologia do identificador. Uma variante pode ter identificadores como barcode, QR ou RFID no futuro.

RFID é explicitamente **adiado** até existir necessidade/custo-benefício real.

QR interno pode abrir a variante no app, mas código 1D continua importante para compatibilidade e velocidade de PDV.

## Tecnologia

Preferência atual:

```text
React Native
Expo
TypeScript
```

Motivos:

- sinergia de linguagem com backend/web planejados;
- ecossistema móvel maduro;
- câmera/barcode/uploads/notificações;
- capacidade de compartilhar contratos/SDK sem compartilhar UI à força.

Antes de congelar a escolha, ADR deve testar câmera, scanner, offline/SQLite, impressão/etiquetas quando relevante, updates, segurança e distribuição.

## Monorepo possível

Estrutura futura possível, não decisão final:

```text
apps/
├── api/
├── admin-web/
├── mobile/
└── pos/
packages/
├── domain/
├── contracts/
├── validation/
├── api-client/
└── observability/
```

Não migrar repos existentes só para atingir estética de monorepo.

## Segurança mobile

- secrets de marketplace/IA nunca no app;
- tokens com armazenamento seguro apropriado;
- sessões revogáveis;
- TLS;
- least privilege;
- permissões de câmera somente quando necessárias;
- proteção contra IDOR/BOLA no servidor;
- não confiar em `organizationId` enviado pelo cliente;
- uploads validados no servidor;
- não logar PII/secrets;
- considerar dispositivo perdido e logout remoto.

## QA mobile

Critical journeys:

1. scan de produto conhecido;
2. scan desconhecido → cadastro;
3. produto sem código → geração interna;
4. cadastro com variantes;
5. fotos por variante;
6. inventário com divergência;
7. transferência;
8. operação offline + replay sem duplicação;
9. upload interrompido/retry;
10. tenant/store authorization;
11. perda de sessão;
12. câmera negada com fallback útil.

Testar aparelhos/tamanhos reais quando implementação começar; emulação não substitui validação de câmera e scanner.

## Diferencial SaaS pretendido

```text
Mercadoria chegou
→ escanear/gerar código
→ fotografar
→ IA sugere conteúdo
→ confirmar variantes/estoque
→ gerar etiqueta
→ publicar no site/canais
→ vender
→ estoque/comissão/analytics atualizam
```

O valor precisa ser medido em redução de tempo/retrabalho e qualidade dos dados, não apenas demonstrado em mockups.

## Relacionados

- [Códigos de barras e scanner](barcodes-and-scanning.md)
- [Pipeline de mídia](../ai/media-pipeline.md)
- [Governança de IA](../ai/ai-governance.md)
- [Estoque](../domain/inventory.md)
- [Identidade e permissões](../domain/identity-employees-permissions.md)
- [Omnichannel](../commerce/omnichannel.md)
