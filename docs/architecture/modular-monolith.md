# Monólito Modular

## Motivação

O Mora Core possui domínio amplo, mas inicialmente uma única equipe/produto. Microserviços criariam custo de rede, deploy, observabilidade, consistência distribuída e debugging sem benefício comprovado.

## Módulos propostos

- `identity`
- `organizations`
- `catalog`
- `inventory`
- `purchasing`
- `sales`
- `cash`
- `commissions`
- `customers`
- `commerce`
- `storefront`
- `media`
- `ai`
- `integrations`
- `reporting`
- `audit`
- `billing`

## Regras de fronteira

1. Módulo expõe contratos explícitos; não acessar tabelas internas de outro módulo diretamente sem decisão arquitetural.
2. Não criar pasta `shared` como depósito. Compartilhado deve conter apenas conceitos realmente transversais e estáveis.
3. Dependências direcionais devem ser claras e sem ciclos.
4. Regras críticas pertencem ao módulo dono do conceito.
5. Integração entre módulos pode usar chamada de aplicação direta ou eventos internos; eventos entram quando desacoplamento real justificar.

## Composition root

O bootstrap deve apenas carregar configuração, montar dependências, registrar módulos, iniciar observabilidade e subir processos. Não deve conter regra de negócio.

## Extração futura

Um módulo só é candidato a serviço independente quando possuir contrato estável, necessidade operacional clara, dados/ownership bem definidos e observabilidade suficiente para suportar distribuição.
