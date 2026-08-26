# Governança de IA

## Regra central

**Product Truth** é determinístico e verificado. **Generated Content** é proposta derivada.

IA pode sugerir: nome, descrição, categoria, tags, título por canal, classificação visual, melhoria de foto e criativos.

IA não decide: preço, estoque, disponibilidade real, desconto autorizado, comissão, tenant, imposto, autorização, venda ou pagamento.

## Human-in-the-loop

Conteúdo comercial gerado passa por revisão proporcional ao risco antes de publicação. Dados estruturados fornecidos pelo usuário têm precedência sobre inferência visual.

## Structured output

Respostas usadas pelo sistema devem seguir schema validado, com campos como `suggestion`, `confidence`, `warnings`, `sources/factsUsed` quando aplicável.

## Proveniência

Registrar provider, model/version quando disponível, prompt/template version, source assets/facts, timestamp, actor, aprovação e custo.

## Segurança

Prompts, imagens, documentos e outputs são não confiáveis. Ferramentas têm allowlist, autorização, budgets e timeout. Nenhum modelo recebe acesso irrestrito a banco, shell ou credenciais.

## Avaliação

Golden dataset, casos adversariais, grounding, hallucination, formato, fidelidade visual, tool security, custo/latência e avaliação humana.

## SaaS

IA é feature medível por tenant; quotas e custo devem ser controlados. Dados de um tenant não podem ser reutilizados para outro sem base legal/consentimento apropriado.
