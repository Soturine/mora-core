# Personas e Jornadas Críticas

> **Status:** personas operacionais e jornadas planejadas. Devem ser ajustadas após Discovery com usuários reais.

## Objetivo

Garantir que o Mora Core seja desenhado por tarefas e responsabilidades, não por uma tela genérica com tudo para todos.

Cada persona tem:

- objetivos;
- permissões;
- dados necessários;
- dados que não deveria ver;
- jornadas críticas;
- riscos operacionais.

---

# 1. Proprietário

## Precisa

- visão consolidada por organização/loja/canal;
- faturamento;
- ticket médio;
- estoque;
- mais vendidos;
- produtos parados;
- caixas;
- divergências;
- comissões;
- equipe;
- integrações;
- custos/margens quando disponíveis;
- alertas acionáveis.

## Pode precisar autorizar

- desconto elevado;
- cancelamento extraordinário;
- sangria alta;
- ajuste de estoque;
- alterações de comissão;
- mudanças de configuração/usuário;
- conexões de marketplace.

## Jornada crítica: acompanhar o dia

```text
login
→ selecionar organização
→ dashboard consolidado
→ identificar exceção
→ abrir detalhe
→ tomar ação/autorizar
```

---

# 2. Gerente

## Precisa

- operar loja(s) autorizada(s);
- acompanhar vendas/equipe;
- catálogo;
- publicação;
- estoque;
- inventário;
- transferências;
- caixa;
- aprovações;
- relatórios operacionais.

## Não necessariamente precisa

- administração global do SaaS;
- billing da plataforma;
- secrets de integração;
- dados de outras organizações.

---

# 3. Vendedor

## Precisa

- login próprio;
- localizar produto;
- ver disponibilidade autorizada;
- registrar/ser atribuído à venda;
- aplicar desconto dentro do limite;
- consultar suas últimas vendas;
- visualizar comissão própria se a política permitir;
- metas próprias quando utilizadas.

## Não deve receber automaticamente

- custo das peças;
- margem total;
- comissão de colegas;
- configuração de plano de comissão;
- faturamento consolidado;
- acesso a outra loja/tenant sem escopo.

## Jornada: venda assistida

```text
login
→ selecionar loja
→ localizar/scan produto
→ confirmar variante
→ montar venda
→ aplicar desconto permitido
→ encaminhar/finalizar pagamento
→ venda concluída
→ comissão provisionada quando elegível
```

Seller e cashier podem ser diferentes.

---

# 4. Caixa

## Precisa

- abrir sessão;
- receber venda;
- registrar meios de pagamento;
- lidar com operações permitidas;
- sangria/suprimento conforme autorização;
- fechamento;
- reconciliação;
- informar divergência.

## Jornada: fechamento

```text
sessão aberta
→ encerrar vendas
→ contar dinheiro
→ reconciliar meios eletrônicos
→ informar valores
→ sistema compara
→ justificar divergência
→ aprovação se necessária
→ fechar sessão
```

Fechamento cego pode ocultar o esperado antes da submissão.

---

# 5. Operador de estoque

## Precisa

- receber mercadoria;
- scan;
- cadastrar produto/variante conforme permissão;
- tirar fotos;
- gerar etiqueta;
- inventariar;
- transferir;
- registrar perda/avaria;
- solicitar ajuste.

## Jornada: recebimento mobile

```text
selecionar loja/local
→ scan
→ produto existe?
   ├── sim: abrir variante
   └── não: cadastro
→ conferir grade
→ fotos
→ quantidades
→ registrar recebimento
→ imprimir etiquetas
→ enviar para revisão/publicação
```

## Jornada: inventário

```text
abrir contagem
→ scan repetido
→ salvar progresso
→ comparar esperado x contado
→ revisar divergências
→ aprovação
→ gerar ajuste
```

Contagem não altera saldo silenciosamente.

---

# 6. Contador / apoio fiscal

## Precisa

Somente o necessário para a finalidade autorizada:

- documentos/relatórios;
- exportações fiscais;
- conferência;
- informações de CNPJ/estabelecimento.

Não deve receber privilégios de venda, estoque ou administração apenas por ter acesso contábil.

---

# 7. Cliente final do storefront

## Precisa

- navegação rápida;
- categorias;
- fotos fiéis;
- produto individual;
- variantes válidas;
- disponibilidade conforme política;
- preço verificado quando publicado;
- descrição;
- contato/compra;
- acessibilidade;
- boa experiência mobile.

## Jornada: descobrir e comprar/consultar

```text
homepage/categoria
→ card
→ página de produto
→ escolher variante válida
→ verificar disponibilidade
→ CTA WhatsApp/checkout futuro
```

Zoom avançado fica na página individual, não no card.

---

# 8. Merchant SaaS / proprietário de outra empresa

## Precisa

- criar conta;
- criar organização;
- lojas/marcas;
- importar dados;
- convidar equipe;
- configurar permissões;
- conectar integrações;
- opcionalmente criar site;
- iniciar operação sem depender de configuração manual pelo time Mora Core.

## Jornada: onboarding

```text
conta
→ organização
→ estrutura comercial
→ importar/cadastrar catálogo
→ equipe
→ integrações
→ site opcional
→ validação
→ go-live
```

---

# 9. Suporte da plataforma

Persona futura e de alto risco.

Se existir suporte administrativo, ele precisa:

- acesso mínimo;
- tenant explicitamente selecionado;
- justificativa;
- auditoria;
- duração limitada;
- nenhuma leitura arbitrária de dados sensíveis sem base/necessidade.

Impersonation não é requisito automático.

---

# Jornadas transversais críticas

## Produto novo

```text
scan/novo
→ Product Draft
→ variantes válidas
→ mídia
→ estoque
→ revisão
→ publish
→ Novidades
```

## Venda física

```text
vendedor
→ itens
→ pagamento
→ conclusão atômica
→ InventoryMovement
→ CashMovement
→ CommissionEntry
→ analytics
```

## Devolução

```text
localizar venda
→ validar elegibilidade
→ registrar itens
→ fiscal/pagamento conforme caso
→ InventoryMovement de retorno
→ CommissionEntry de reversão
→ audit
```

## Transferência

```text
origem
→ itens/quantidades
→ autorização
→ TRANSFER_OUT
→ trânsito opcional
→ recebimento
→ TRANSFER_IN
```

## Publicação multicanal

```text
Product Truth
→ selecionar canais
→ compliance por canal
→ jobs independentes
→ external IDs
→ health/sync
```

## Pedido marketplace

```text
webhook/poll
→ autenticar/dedupe
→ canonical Order
→ reserva
→ fiscal
→ fulfillment
→ tracking
→ atualização de estoque/canais
```

## Operação offline mobile

```text
atividade local
→ operationId
→ outbox
→ rede retorna
→ sync idempotente
→ conflito explícito
```

---

# Critérios de experiência por persona

- mostrar somente ações relevantes;
- contexto de organização/loja sempre claro;
- não depender de memorizar códigos internos;
- erros dizem o que aconteceu e como corrigir;
- ações sensíveis deixam rastro;
- operações longas mostram progresso/status;
- offline não finge sucesso remoto;
- notificações são acionáveis, não ruído;
- acessibilidade faz parte da jornada;
- mobile crítico é validado em aparelho real.

## Questões para Discovery

- vendedor e caixa são papéis realmente separados?
- uma venda pode ser dividida entre vendedores?
- vendedora quer/ pode ver comissão durante o mês?
- quais dashboards proprietários realmente usam?
- quem publica produto?
- quem cadastra/recebe mercadoria?
- quais ações exigem autorização?
- contador precisa de acesso direto ou apenas exportação?

## Relacionados

- [Discovery](../discovery/operational-discovery.md)
- [Identidade/permissões](../domain/identity-employees-permissions.md)
- [Vendas/caixa/comissão](../domain/sales-cash-commissions.md)
- [Mobile](../mobile/mobile-app.md)
- [Storefront](storefront.md)
