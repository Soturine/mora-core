# Privacidade, LGPD e Proteção de Dados

> **Status:** baseline técnico/organizacional. Não é parecer jurídico. Política de Privacidade, Termos, DPA e bases legais finais exigem revisão profissional antes do SaaS público.

## Objetivo

Desenvolver o Mora Core com minimização, finalidade, acesso e lifecycle desde o início, evitando tentar “adicionar LGPD” depois que dados já estiverem espalhados.

## Princípios

- coletar apenas o necessário;
- finalidade explícita;
- acesso mínimo;
- tenant isolation;
- segurança proporcional;
- transparência;
- portabilidade/export quando aplicável;
- retenção/deleção definida;
- subprocessadores conhecidos;
- evidência de operações sensíveis.

## Categorias de titular

- usuários da plataforma;
- funcionários dos clientes;
- proprietários/administradores;
- clientes finais das lojas;
- fornecedores/contatos;
- pessoas em fotos/mídia;
- suporte/representantes.

## Inventário de dados

Antes do SaaS público, manter data inventory:

```text
categoria
campos
finalidade
origem
base/justificativa
módulo
quem acessa
onde armazena
subprocessadores
retenção
deleção/export
```

## Funcionários

`Employee` não é desculpa para guardar folha/documentos sensíveis que o Core não usa.

Necessário inicialmente pode ser:

- nome;
- vínculo/status;
- loja;
- comissão/role;
- referência de usuário.

Dados trabalhistas adicionais entram somente por requisito.

## Clientes finais

Venda pode ser anônima quando permitido e suficiente. Quando identificar cliente, coletar apenas dados necessários para venda/fiscal/relacionamento autorizado.

Marketing/CRM precisa de finalidade e mecanismos adequados; não reutilizar automaticamente dado fiscal para propaganda.

## Mídia e imagem

Fotos de produto sem pessoa têm risco diferente de fotos com pessoas reais.

Para modelos reais:

- direito de imagem/consentimento/contrato conforme caso;
- finalidade;
- retenção;
- canal de publicação;
- possibilidade de revogação quando aplicável.

Conteúdo com crianças exige cuidado adicional e política específica.

Modelo virtual sintético deve ser marcado/provenance, evitando confundir com pessoa real quando relevante.

## IA e subprocessadores

Antes de enviar dado a provider:

- minimizar;
- verificar termos/retention;
- conhecer região/processamento quando necessário;
- evitar PII desnecessária;
- configurar opções de treinamento/retention quando disponíveis;
- documentar subprocessador.

Dados de um tenant não podem ser usados para outro sem base adequada.

## Marketplaces

Cada marketplace recebe apenas o necessário ao listing/order/fulfillment. Dados de pedidos importados permanecem sujeitos a finalidade e políticas do canal.

## Logs

Não registrar:

- senha;
- token;
- cartão;
- PII completa por padrão;
- descrição livre com dados sensíveis sem redaction.

Debug deve usar IDs e metadata mínima.

## Analytics

Preferir agregação/pseudonimização quando identificação não é necessária.

Business analytics de vendedor pode usar employeeId interno com acesso restrito.

## Direitos e solicitações

SaaS público precisa de fluxo para solicitações aplicáveis:

- confirmação/acesso;
- correção;
- portabilidade;
- oposição/restrição quando cabível;
- eliminação onde legalmente permitida.

A resposta precisa considerar que vendas/documentos fiscais podem ter retenção obrigatória.

## Export do tenant

Owner autorizado pode solicitar export operacional. Isso é diferente de uma solicitação individual de titular; cada fluxo tem escopo/permissão próprio.

## Deleção

Mapear:

- DB;
- storage;
- derived media;
- cache/CDN;
- search;
- analytics;
- export temp;
- provider externo;
- backup.

Backup pode expirar conforme retenção em vez de edição seletiva imediata, se política/lei permitir e estiver documentado.

## Retenção

Não inventar prazos universais. Tabela final precisa de categoria + obrigação contratual/legal.

Dados temporários, como export e upload processing, devem ter TTL curto.

## Multi-tenancy

Privacidade depende de isolamento. Testar:

- export;
- search;
- logs;
- support;
- cache;
- background jobs;
- object storage.

## Suporte

Ferramenta de suporte não pode permitir navegar dados de qualquer tenant sem rastreabilidade. Acesso elevado futuro exige necessidade, authorization, reason, duração e audit.

## Incidente de dados

Runbook precisa permitir:

- conter;
- descobrir tenants/dados afetados;
- preservar evidência;
- rotacionar credenciais;
- avaliar impacto;
- comunicação/notificação conforme obrigação;
- postmortem.

## Privacy by design no desenvolvimento

Checklist em feature nova:

- precisa de PII?
- qual finalidade?
- pode usar ID interno?
- quem vê?
- entra em log?
- entra em IA/provider?
- precisa export/deletion?
- retention?
- tenant boundary?
- threat model mudou?

## Documentos legais futuros

Antes de SaaS público:

- Política de Privacidade;
- Termos de Uso;
- contrato/termos SaaS;
- DPA/encargos de controlador-operador conforme relação;
- subprocessadores;
- política de cookies/analytics se aplicável;
- canal de privacidade;
- processo interno de incidentes/direitos.

Não copiar template genérico sem revisar produto real.

## Segurança

Ver [Arquitetura de Segurança](security-architecture.md). LGPD não é atingida apenas com criptografia e scanning.

## Relacionados

- [Data lifecycle](../data/data-lifecycle.md)
- [IA](../ai/ai-governance.md)
- [Multi-tenancy](../saas/multitenancy.md)
- [Incidentes](../operations/incident-recovery.md)
