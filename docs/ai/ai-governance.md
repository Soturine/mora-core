# Governança de IA

> **Status:** política arquitetural planejada para qualquer feature baseada em modelos de IA.

## Princípio central

**IA é uma ferramenta de sugestão e transformação, não fonte de verdade para operações críticas.**

Separação:

- **Product Truth:** fatos verificados/determinísticos;
- **Generated Content:** conteúdo derivado/sugerido.

## IA pode ajudar em

- título de produto;
- descrição;
- tags;
- classificação;
- sugestão de categoria;
- extração visual assistida;
- melhoria de foto;
- recorte/fundo;
- modelo virtual;
- copy por canal;
- resumo de erros/logs;
- agrupamento/anomalia AIOps;
- sugestão de runbook.

## IA não decide sozinha

- preço final autorizado;
- saldo de estoque;
- disponibilidade física;
- imposto;
- emissão fiscal;
- desconto privilegiado;
- comissão;
- autorização/permissão;
- tenant;
- pagamento;
- exclusão de dados;
- remediação destrutiva de infraestrutura.

## Human-in-the-loop

Nível de revisão proporcional ao risco.

### Baixo risco

Exemplo: sugerir tags internas. Pode ser aceito em lote com validação posterior.

### Médio

Descrição comercial, categoria e background removal: revisão antes de publicação conforme confiança/política.

### Alto

Modelo virtual, conteúdo infantil, alteração visual significativa, ação operacional: revisão obrigatória e regras de canal.

## Structured output

Quando output alimenta software, exigir schema validado.

Exemplo:

```json
{
  "suggestions": [],
  "factsUsed": [],
  "warnings": [],
  "confidence": 0.0
}
```

Texto livre nunca é executado como comando privilegiado.

## Grounding

Prompts de descrição devem receber dados estruturados verificados:

```text
nome
categoria
cor
material
medidas
atributos
```

Modelo não pode preencher campos desconhecidos fingindo certeza.

Distinguir:

- `known`;
- `suggested`;
- `unknown`.

## Proveniência

Registrar quando relevante:

```text
provider
model/version
prompt/template version
source facts/assets
request ID
organization
actor
createdAt
approval status
approvedBy
cost/usage
```

Isso permite auditar/regenerar/comparar versões.

## AI Gateway

Nenhum cliente chama provider diretamente.

```text
Web/Mobile/Worker
→ Mora Core
→ AI Gateway
→ Provider Adapter
```

Gateway aplica:

- auth/tenant;
- policy;
- quota;
- budget;
- timeout;
- request/response size;
- schema validation;
- redaction;
- provider routing;
- provenance;
- observability;
- kill switch.

## Provider abstraction

Evitar acoplar domínio a um modelo específico. Cada capability pode ter contrato próprio:

```text
generateProductDescription()
enhanceProductImage()
generateVirtualModel()
classifyProduct()
```

Não criar `AIService.doAnything(prompt)` como API interna universal sem controle.

## Prompt injection

Inputs como descrição, imagem, documento e página externa são não confiáveis.

Modelo nunca recebe automaticamente:

- shell;
- banco irrestrito;
- secret manager;
- marketplace token;
- ferramentas administrativas.

Tool use, quando houver, usa allowlist, authorization e confirmação/policy.

## Privacidade

Antes de enviar dado ao provider:

- minimizar PII;
- verificar necessidade;
- conhecer política/contrato de dados;
- respeitar tenant boundary;
- não usar conteúdo de um cliente para outro sem base adequada;
- tratar imagens de pessoas/crianças com cuidado adicional.

## Retenção

Definir por provider/capability:

- request metadata;
- prompt/output;
- imagem;
- logs;
- provenance.

Não registrar payload completo só por conveniência de debug.

## Evals

Toda capability relevante precisa de avaliação própria.

### Texto de produto

- hallucination de material/composição;
- facts coverage;
- idioma/tom;
- campos obrigatórios;
- tamanho;
- conteúdo proibido;
- regressão entre modelos.

### Classificação

- accuracy por categoria;
- confidence calibration;
- unknown handling;
- classes raras;
- imagens ruins.

### Imagem

- cor;
- estampa;
- geometria;
- botões;
- manga/decote;
- fidelidade à variante;
- artefatos;
- conteúdo sensível;
- avaliação humana.

## Golden dataset

Criar dataset interno versionado com produtos reais autorizados/fixtures. Separar treino de avaliação e não vazar dados sensíveis.

## Regressão

Trocar modelo/provider requer eval comparativo. “Modelo novo é melhor” não é evidência.

## Confidence

Confidence do modelo não substitui validação. Pode orientar UX, por exemplo:

```text
alta confiança → sugestão destacada
baixa → pedir confirmação
```

mas não autoriza fato crítico.

## Falhas

Tratar:

- timeout;
- quota;
- provider 5xx;
- output inválido;
- conteúdo recusado;
- latência alta;
- custo anormal;
- versão removida;
- imagem não processável.

Feature de IA deve degradar de forma segura: o lojista ainda consegue cadastrar produto manualmente.

## Custos

Metering por tenant/capability. Definir budgets antes de escala SaaS.

Possíveis limites:

- geração/dia/mês;
- tamanho de imagem;
- pixels;
- tokens;
- concurrency;
- custo monetário.

## Segurança de supply chain de IA

- SDKs/deps revisados;
- provider endpoints allowlisted;
- keys rotacionáveis;
- nenhum secret em APK/browser;
- model/template changes versionados;
- logs sanitizados.

## AIOps

IA pode:

- resumir incidente;
- correlacionar erros;
- sugerir runbook;
- detectar anomalia.

Não pode:

- fechar incidente automaticamente sem critério;
- apagar dados;
- alterar firewall/infra crítica;
- fazer rollback destrutivo sem checkpoint/policy.

## Compliance de marketplace

Conteúdo gerado deve passar pelas regras atuais de cada canal. Regras mudam; usar compliance engine versionado e revalidar documentação oficial.

## Status e transparência

Mídia/conteúdo deve guardar `aiGenerated` quando aplicável. A UI precisa distinguir original, melhorado e gerado.

## Testes adversariais

- prompt injection em descrição;
- payload gigante;
- imagem malformada;
- cross-tenant asset reference;
- provider returning arbitrary HTML/script;
- replay de generation job;
- quota race;
- output estruturado inválido.

## Relacionados

- [Pipeline de mídia](media-pipeline.md)
- [Segurança](../security/security-architecture.md)
- [Billing/quotas](../saas/billing-entitlements.md)
- [SRE/AIOps](../operations/sre-aiops.md)
