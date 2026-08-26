# Mora Core como Projeto, Case e Portfólio

> **Objetivo:** preservar uma narrativa profissional e honesta do projeto para currículo, portfólio, entrevistas, parceiros e família.

## Regra principal

Nunca apresentar roadmap como funcionalidade implementada.

Vocabulário:

- `implementado` — existe e foi validado;
- `parcial` — parte do escopo existe;
- `experimental` — existe, mas ainda não é estável;
- `planejado` — documentado, ainda não construído;
- `não validado` — falta evidência;
- `adiado` — conscientemente postergado.

## Posicionamento

Nome de projeto/produto:

> **Mora Core — Plataforma de Gestão e Comércio Omnichannel para Varejo**

Em inglês, quando necessário:

> **Mora Core — Retail ERP & Omnichannel Commerce Platform**

A descrição deve evoluir conforme o software realmente existir.

## Hoje — fase documentation-first

Forma correta:

> Projeto autoral de uma plataforma SaaS-ready para varejo, atualmente em fase de discovery/arquitetura, com domínio planejado para catálogo, variantes, estoque, vendas, caixa, comissões, mobile, sites e integrações omnichannel.

Não dizer:

> “ERP completo usado em produção”

antes disso ser verdade.

## Quando Foundation existir

Exemplo:

> Desenvolvi o núcleo de uma plataforma de varejo multi-tenant com catálogo de variantes, ledger de estoque, RBAC, auditoria e API transacional em PostgreSQL.

Somente se todos esses itens estiverem realmente implementados/validados.

## Quando entrar em piloto nas lojas

A narrativa ganha muito peso:

> Projetei e desenvolvi uma plataforma utilizada em operação real por duas lojas, centralizando catálogo, SKUs, estoque e fluxos operacionais, com integração aos storefronts.

Acrescentar vendas/PDV/comissão apenas quando estiverem no piloto.

## Quando estiver em produção

Usar métricas reais:

- número de SKUs;
- lojas;
- usuários;
- vendas processadas;
- movimentos de estoque;
- tempo de cadastro antes/depois;
- divergência de inventário;
- tempo de publicação;
- disponibilidade;
- redução de retrabalho;
- canais conectados.

Exemplo futuro hipotético — **não usar antes de medir**:

> Reduziu o tempo médio de entrada e publicação de mercadorias de X para Y, centralizando Z SKUs entre duas lojas e canais digitais.

## Por que é um bom case técnico

O mesmo projeto permite demonstrar:

- modelagem de domínio;
- arquitetura modular;
- PostgreSQL/transações;
- concorrência/idempotência;
- REST/OpenAPI;
- auth/RBAC/multi-tenancy;
- React/mobile;
- câmera/barcode/offline;
- storage/media;
- IA com guardrails;
- PIM/OMS;
- webhooks/OAuth;
- fiscal/payments adapters;
- testing;
- DevSecOps;
- SRE/AIOps;
- observabilidade;
- accessibility;
- release engineering.

## Estrutura recomendada de case

### 1. Problema

```text
lojas físicas
+ ERP atual
+ sites
+ estoque
+ fotos
+ comissões/caixa
+ canais externos
→ retrabalho e fragmentação
```

### 2. Discovery

Mostrar que requisitos vieram de operação real, não de tutorial.

### 3. Solução

Diagrama simples do Core e clientes.

### 4. Decisões

- por que monólito modular;
- por que ledger de estoque;
- por que ProductVariant;
- por que seller != cashier;
- por que adapters;
- por que multi-tenant desde cedo sem microservices.

### 5. Segurança

Tenant isolation, secrets, audit, idempotência, backups.

### 6. Qualidade

Testes reais, CI, migrations, release SHA, observability.

### 7. Evolução

```text
sites estáticos
→ repository boundary
→ Mora Core Catalog API
→ mobile/PDV
→ omnichannel
```

### 8. Resultados

Somente números medidos.

### 9. Aprendizados/trade-offs

Mostrar decisões adiadas e por quê.

## Demonstração em vídeo

Uma future demo forte:

```text
scan produto
→ cadastro/grade
→ tirar foto
→ IA sugere conteúdo
→ entrada de estoque
→ publicar
→ abrir site
→ vender
→ saldo/comissão/dashboard atualizam
```

Um vídeo de uma jornada real vale mais que muitas screenshots desconectadas.

## README e recrutador

O README precisa permitir três níveis de leitura:

1. 30 segundos — o que é;
2. 5 minutos — arquitetura/capacidades/roadmap;
3. aprofundamento — `/docs`.

É por isso que existe [Visão Executiva](../product/executive-overview.md) e documentação técnica separada.

## Privacidade no portfólio

Não expor:

- dados de clientes;
- vendas reais identificáveis;
- comissão individual;
- credentials;
- documentos fiscais;
- endereço/telefone pessoal além do já público e necessário;
- screenshots administrativas com PII.

Usar dados sintéticos/anonimizados nas demos públicas.

## Código proprietário e portfólio

Repo público pode demonstrar arquitetura, mas **publicação não concede automaticamente licença de reutilização**. Ainda assim, tornar código proprietário público expõe implementação e facilita cópia, então a estratégia de visibilidade deve ser revisada quando código comercial começar.

É possível manter um repositório privado e um case público separado no futuro.

## Currículo — exemplos por estágio do projeto

### Planejamento/arquitetura

> Mora Core — projeto autoral de plataforma SaaS-ready para varejo; modelagem de domínio, arquitetura multi-tenant, catálogo/estoque e estratégia omnichannel.

### Implementado

Trocar “planejamento” pelos módulos realmente entregues.

### Produção

Adicionar operação real e métricas verificáveis.

## Entrevista

Perguntas que o projeto deve permitir responder com evidência:

- como evitou overselling?
- por que não microservices?
- como tenant isolation é testado?
- como funciona comissão após devolução?
- como sincroniza webhook duplicado?
- por que original de foto é preservado?
- como faz deploy/migration segura?
- como mede qualidade da IA?
- como restaura backup?

Se a resposta depende de “a IA gerou o código”, a documentação/entendimento não está suficiente.

## Relacionados

- [Visão Executiva](../product/executive-overview.md)
- [Roadmap](../roadmap/roadmap.md)
- [Cobertura documental](../roadmap/documentation-coverage.md)
- [Engineering Constitution](../engineering/constitution.md)
