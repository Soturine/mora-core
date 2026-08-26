# Segurança — Mora Core

A segurança do Mora Core é parte da arquitetura e da Definition of Done, não uma etapa final.

## Estado

O projeto ainda está em fase de documentação/arquitetura. Nenhuma alegação de conformidade é feita neste momento.

## Princípios

- secure-by-design e secure-by-default;
- menor privilégio;
- fail-closed em decisões sensíveis;
- defesa em profundidade;
- isolamento forte de tenant;
- autorização no recurso/objeto;
- secrets apenas no backend/secret manager;
- dados e outputs externos tratados como não confiáveis;
- limites explícitos de recursos;
- trilha de auditoria para operações sensíveis.

Referências de projeto: OWASP ASVS, OWASP API Security Top 10, NIST SSDF e boas práticas equivalentes, sem alegação automática de compliance.

## Dados especialmente sensíveis

Prioridade de proteção elevada para: credenciais, dados pessoais de clientes/funcionários, vendas, comissões, movimentações financeiras, documentos fiscais, tokens de marketplace, mídia privada e trilhas de auditoria.

Dados de cartão não devem ser armazenados pelo Mora Core quando provedores/TEF puderem assumir essa responsabilidade.

## Multi-tenancy

Nunca autorizar acesso apenas porque o recurso existe ou porque o cliente enviou um `organizationId`. O contexto autorizado deve ser derivado da identidade autenticada e das memberships/permissões. Testes adversariais entre tenants são obrigatórios antes de SaaS público.

## Uploads e mídia

Validar tamanho, MIME real, dimensões, quantidade e tipo. Não confiar em extensão. Processar em ambiente isolado quando aplicável. Metadados sensíveis devem ser removidos quando não necessários. Original e derivados precisam de política de lifecycle.

## Integrações

Tokens OAuth/API devem ser criptografados no backend, com escopo mínimo, rotação/revogação e redaction em logs. Webhooks exigem validação de assinatura/origem quando o provedor suportar, dedupe e processamento idempotente.

## IA

Prompts, imagens, documentos e respostas de modelos são inputs não confiáveis. IA nunca pode ampliar permissões, modificar tenant, executar SQL arbitrário, decidir preço/estoque/tributação, ou publicar afirmações não verificadas sem guardrails e checkpoint humano conforme risco.

## Vulnerabilidades

Enquanto o projeto estiver sob desenvolvimento fechado, reporte vulnerabilidades diretamente ao mantenedor por canal privado apropriado. Não publique segredos, tokens, dados pessoais ou detalhes exploráveis em issues públicas.

## Incidentes

Incidentes futuros seguem `docs/operations/incident-recovery.md`, com contenção, preservação de evidência, rotação de credenciais, comunicação proporcional, recuperação, validação e postmortem sem culpabilização.
