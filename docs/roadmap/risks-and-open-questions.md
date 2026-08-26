# Riscos, Assunções e Questões em Aberto

## Riscos de produto
- ampliar escopo cedo demais;
- construir features SaaS sem validar operação real;
- IA gerar conteúdo enganoso;
- sites/marketplaces criarem expectativa de estoque instantâneo sem contrato de freshness;
- complexidade fiscal brasileira.

## Riscos técnicos
- isolamento cross-tenant;
- races de estoque/reserva;
- duplicidade de webhook/pagamento;
- perda de mídia/offline sync;
- integrations drift;
- migration destrutiva;
- custos de IA/storage/egress.

## Questões que exigem discovery
- quantos CNPJs/legal entities existem na operação inicial;
- como comissão funciona na prática;
- seller vs cashier e vendas compartilhadas;
- política de trocas/devoluções;
- depósitos/locais de estoque;
- impressoras, scanners, TEF/Pix e terminais;
- política fiscal/contador;
- confiabilidade de internet;
- quais marketplaces serão realmente usados primeiro;
- necessidade real de checkout próprio;
- categorias e atributos recorrentes.

## Decisões ainda não congeladas
- framework backend exato (Fastify vs NestJS ou alternativa);
- ORM/query layer;
- provedor cloud;
- auth provider vs auth própria;
- provider fiscal;
- queue/storage concretos;
- React Native/Expo após spike.

Tudo isso exige ADR antes de implementação estrutural.
