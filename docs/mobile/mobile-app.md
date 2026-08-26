# Aplicativo Mobile

## Papel

O mobile é ferramenta operacional para câmera, scanner, inventário, recebimento e consulta rápida. Não é apenas uma cópia responsiva do admin.

## Direção técnica

React Native + Expo + TypeScript é a preferência atual por sinergia com o ecossistema TS, sujeita a ADR antes da implementação.

## Capacidades

- autenticação e seleção de organização/loja;
- consulta de produto/estoque;
- scanner de código;
- cadastro de produto e variantes;
- fotos e uploads resilientes;
- geração de código interno/etiqueta;
- recebimento e transferências;
- inventário;
- dashboard conforme role;
- fechamento de caixa onde apropriado;
- notificações acionáveis.

## Offline

Fluxos de inventário/rascunho podem usar SQLite local + outbox. Cada operação possui `operationId` idempotente. Dados financeiros/autorizações críticas continuam sujeitos ao servidor.

## Uploads

Original é salvo primeiro; derivados e IA processam em background. A pessoa pode continuar trabalhando enquanto o job executa.

## Permissões

Câmera apenas quando necessária; não solicitar permissões globais antecipadamente. Nenhum secret de integração fica no app.
