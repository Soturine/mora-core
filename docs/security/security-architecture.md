# Arquitetura de Segurança e Threat Model

## Ativos críticos

Credenciais, tenant data, vendas, estoque, caixa, comissão, clientes, tokens OAuth, documentos fiscais, mídia privada, backups e audit logs.

## Ameaças principais

- BOLA/IDOR e vazamento cross-tenant;
- privilege escalation;
- credential stuffing/session theft;
- injection/XSS/CSRF/SSRF;
- upload malicioso;
- webhook spoofing/replay;
- overselling por race;
- fraude em desconto/cancelamento/comissão/caixa;
- secrets expostos;
- supply-chain compromise;
- prompt injection e abuso de ferramentas de IA;
- ransomware/perda de dados;
- abuso de recursos/custo.

## Controles

### Identidade
Senha com hash moderno, sessão/token seguro, MFA para perfis privilegiados, revogação de dispositivos e rate limiting.

### Autorização
RBAC + escopo de organização/loja + autorização do objeto. Policies centralizadas e fail-closed.

### Tenant isolation
Scoping server-side em toda query/write; testes adversariais cross-tenant; RLS como defesa adicional quando adotada.

### Dados
TLS, encryption at rest do provedor, criptografia adicional para credenciais/tokens, backups protegidos, retenção mínima e redaction em logs.

### Uploads
Budget de bytes/dimensões/quantidade; MIME real; nomes gerados; storage separado; processamento seguro; signed URLs quando privadas.

### Integrações
Assinatura/HMAC quando disponível, OAuth com scopes mínimos, inbox dedupe, timeouts, response-size limits, allowlists quando aplicável.

### Operações sensíveis
Audit log, motivo, step-up approval conforme risco, idempotência e transação.

## Security gates

SAST, SCA, secret scanning, CodeQL, dependency review, IaC/container scanning quando essas superfícies existirem. Sem claim de compliance automático.

## Privacidade/LGPD

Minimização, finalidade, acesso limitado, exportação, retenção e deleção precisam ser documentados por categoria de dado antes de SaaS público.
