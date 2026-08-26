# Contribuindo com o Mora Core

Mora Core é software proprietário. Contribuições não são automaticamente aceitas e não transformam o projeto em open source.

## Antes de começar

Leia:
- `AGENTS.md`;
- `docs/index.md`;
- `docs/engineering/constitution.md`;
- documentação específica do domínio alterado;
- ADRs relacionados.

## Fluxo esperado

1. Defina objetivo e critérios de aceitação.
2. Identifique regras de negócio, riscos, permissões, concorrência e out-of-scope.
3. Faça alteração pequena e coerente.
4. Adicione/ajuste testes proporcionais ao risco.
5. Atualize documentação/contratos/migrations se necessário.
6. Rode gates focados.
7. Faça commit lógico.
8. Faça push remoto real.
9. Aguarde/acompanhe CI assíncrona sem polling excessivo.
10. Antes de release, valide o SHA exato.

## Commits

Mensagens curtas e semânticas, por exemplo:
- `feat(catalog): add variant availability rules`
- `fix(inventory): prevent duplicate transfer receipt`
- `docs(architecture): record tenancy decision`
- `test(commissions): cover partial return reversal`

## Qualidade

Não são aceitos como evidência suficiente: muitos testes, coverage alto, build verde isolado, screenshots, ou relatório do próprio agente implementador. A mudança precisa provar comportamento e não degradar arquitetura, segurança, acessibilidade ou dados.

## Dependências

Nova dependência exige justificativa de necessidade, licença, manutenção, segurança, compatibilidade e custo de runtime/build. Lockfile é obrigatório quando a stack suportar.

## Segurança

Nunca commite secrets. `.env.example` contém apenas placeholders. Alterações em auth, tenant isolation, pagamentos, fiscal, migrations, inventory e release são consideradas de risco elevado e exigem validação mais forte.

## Documentação

Current docs representam a verdade atual. Histórico pertence a changelog/release notes. Decisões estruturais relevantes devem ganhar ADR.
