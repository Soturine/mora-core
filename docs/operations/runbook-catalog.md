# Catálogo de Runbooks Futuros

Runbooks executáveis serão escritos quando infraestrutura/serviços existirem. Este arquivo impede que operação crítica fique esquecida.

## Segurança
- secret/token exposto;
- sessão privilegiada comprometida;
- suspeita de acesso cross-tenant;
- upload malicioso.

## Dados
- restore PostgreSQL;
- corrupção/divergência de estoque;
- migration falhou;
- mídia inacessível/perdida.

## Integrações
- Bling indisponível;
- marketplace rate limited;
- webhook backlog;
- token OAuth expirado/revogado;
- reconciliation encontrou drift.

## Commerce
- overselling;
- pagamento duplicado;
- pedido preso;
- fiscal provider indisponível.

## Operação
- rollback de deploy;
- fila crescendo;
- storage/DB perto de limite;
- custo de IA anormal.

Cada runbook futuro deve conter gatilho, impacto, diagnóstico, ações seguras, rollback, validação e escalonamento.
