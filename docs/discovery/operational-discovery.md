# Discovery Operacional — Como a Loja Funciona Hoje

> **Objetivo:** transformar a operação real em requisitos do Mora Core.
>
> Este documento deve ser respondido com proprietários, gerentes, vendedores e quem opera caixa/estoque. Não há resposta “certa”. O importante é registrar **como realmente funciona**, inclusive atalhos, exceções e problemas atuais.

## Como usar

Para cada pergunta, registrar:

- como é feito hoje;
- quem faz;
- em qual sistema/dispositivo;
- frequência/volume;
- problema/dor;
- exceções;
- evidência quando útil: tela, relatório, etiqueta, nota, planilha, procedimento;
- se o comportamento é regra da empresa ou apenas hábito atual.

Não implementar uma funcionalidade importante apenas porque um concorrente possui algo parecido. Primeiro verificar se resolve um problema real da operação.

---

# 1. Estrutura da empresa

- Quantas empresas/CNPJs existem?
- As lojas pertencem ao mesmo CNPJ ou a empresas diferentes?
- Quais nomes/marcas são usados comercialmente?
- Quantas lojas físicas existem?
- Há depósito/estoque separado?
- Um produto pode estar disponível nas duas lojas?
- É permitido transferir mercadoria de uma para outra?
- O estoque é compartilhado ou separado contabilmente?
- Funcionários podem trabalhar nas duas lojas?
- Há mais de um caixa por loja?

Registrar a topologia real antes de congelar `LegalEntity`, `Store` e `StockLocation`.

---

# 2. Sistemas usados hoje

Para cada sistema utilizado:

```text
Nome:
Finalidade:
Quem usa:
Dispositivos:
O que funciona bem:
O que incomoda:
O que não pode ser perdido:
Dados que precisam ser migrados:
```

Perguntas específicas sobre Bling:

- Quais módulos são usados?
- Cadastro de produtos é feito onde?
- Variações cor/tamanho são usadas?
- Como entra estoque?
- XML de fornecedor é importado?
- Como preço é definido?
- Como venda é registrada?
- Usa PDV do Bling?
- Como emite NFC-e/NF-e?
- Como são tratados Pix/cartão/dinheiro?
- Como faz devolução/troca?
- Como fecha caixa?
- Há comissão no sistema ou fora dele?
- Quais relatórios são usados diariamente/mensalmente?
- Existem depósitos/múltiplos estoques?
- Há integrações existentes?
- Há API/app cadastrado?

O objetivo inicial do Mora Core é integrar e aprender antes de substituir funções críticas.

---

# 3. Chegada da mercadoria

Descrever o caminho desde a compra até a peça disponível para venda.

- Quem compra?
- Pedido é registrado antes da mercadoria chegar?
- Há pedido de compra formal?
- Como fornecedor é identificado?
- Chega NF-e/XML?
- Quem confere quantidade?
- Quem confere custo?
- Quem confere tamanhos/cores?
- Há divergência frequente entre pedido e recebido?
- Quando o estoque é considerado disponível?
- A mercadoria recebe etiqueta da loja?
- Quem imprime?
- Qual impressora/modelo/tamanho de etiqueta?
- Produto sem código recebe qual código hoje?
- Quanto tempo normalmente leva para cadastrar uma remessa?

### Fluxo desejado a validar

```text
Mercadoria
→ scan/cadastro
→ variantes
→ fotos
→ recebimento
→ etiquetas
→ revisão
→ publicação
```

Perguntar se essa ordem funciona de verdade ou se precisa variar.

---

# 4. Produto e variantes

Selecionar exemplos reais de diferentes tipos:

- vestido;
- camiseta;
- calça;
- roupa infantil;
- bebê;
- enxoval;
- bolsa/acessório;
- bijuteria/pulseira;
- higiene/cuidado;
- item masculino.

Para cada exemplo, levantar:

- nome;
- departamento;
- categoria/subcategoria;
- marca;
- fornecedor;
- referência do fabricante;
- SKU;
- GTIN/EAN se houver;
- cor;
- tamanho/numeração;
- faixa etária quando aplicável;
- custo;
- preço;
- composição/material;
- coleção/estação;
- fotos;
- peso/dimensões se vendido online;
- dados fiscais necessários.

## Grade

Perguntar como trabalham combinações tamanho × cor.

```text
          P   M   G   GG
Azul      ?   ?   ?   ?
Preto     ?   ?   ?   ?
```

Cada combinação precisa de saldo/código separado?

---

# 5. Código de barras e etiqueta

- Quantos produtos chegam com EAN/GTIN?
- O mesmo EAN identifica todas as cores/tamanhos ou cada variante?
- Produtos sem código recebem etiqueta interna?
- O código atual tem significado humano ou é sequencial?
- Precisa imprimir preço na etiqueta?
- Precisa imprimir descrição/tamanho/cor?
- Há leitor USB?
- Há leitor bluetooth?
- Smartphone seria útil como coletor?
- Código 1D é suficiente?
- QR teria utilidade interna?

Mora Core pode gerar SKU + barcode interno, mas não inventará GTIN oficial.

---

# 6. Fotos e catálogo

- Quem fotografa os produtos hoje?
- Usa celular de quem?
- Fundo/manequim/cabide?
- Quantas fotos por produto?
- Há fotos por cor?
- Onde ficam armazenadas?
- Quem escolhe o que vai para o site/Instagram?
- Quanto tempo gasta editando?
- O que costuma precisar de correção?

## IA — validar interesse real

Avaliar separadamente:

- corrigir iluminação;
- crop/enquadramento;
- remover fundo;
- fundo branco;
- melhorar nitidez;
- gerar descrição;
- sugerir categoria/tags;
- gerar título por marketplace;
- gerar imagem com modelo virtual usando a roupa;
- criar criativo social.

Registrar quais funções seriam úteis e quais seriam desnecessárias.

A foto original precisa permanecer preservada.

---

# 7. Publicação e sites

- Quais produtos devem aparecer online?
- Tudo automaticamente ou só após aprovação?
- Quem aprova?
- Preço online é igual ao físico?
- Mostrar quantidade exata ou apenas “disponível/últimas unidades”?
- Há produtos que nunca devem aparecer no site?
- Como são escolhidas novidades/destaques/promoções?
- Há interesse real em checkout próprio ou WhatsApp basta inicialmente?

## Site builder SaaS

Para um futuro cliente que não tenha site, validar quais configurações seriam suficientes:

- logo;
- cores;
- fontes;
- domínio;
- WhatsApp;
- Instagram;
- endereço;
- temas;
- homepage por blocos;
- categorias;
- políticas/contato.

---

# 8. Venda física

Observar uma venda real do início ao fim.

- Quem inicia a venda?
- Vendedor é escolhido antes ou depois dos itens?
- Quem passa no caixa?
- A mesma pessoa normalmente vende e recebe?
- É possível uma venda ser dividida entre vendedores?
- Como busca produto?
- Leitor é usado?
- Desconto pode ser dado por quem?
- Existe limite de desconto?
- Quem autoriza exceção?
- Como cliente paga?
- Venda pode ter múltiplos meios de pagamento?
- Como comprovante/nota é entregue?
- Quanto tempo leva uma venda normal?
- O que mais atrasa o atendimento?

---

# 9. Pagamentos

Registrar meios realmente aceitos:

- dinheiro;
- Pix;
- débito;
- crédito;
- parcelamento;
- outros.

Para cartão/Pix:

- qual adquirente/maquininha?
- TEF é usado?
- valor é digitado manualmente?
- pagamento volta automaticamente para o PDV?
- existe conciliação?
- taxas são acompanhadas?
- há mais de uma maquininha?

Não trazer dados de cartão para o escopo do Mora Core sem necessidade.

---

# 10. Caixa

- Quem abre?
- Qual fundo inicial?
- Há caixa por funcionário ou compartilhado?
- Há troca de turno?
- Como registra sangria?
- Como registra suprimento?
- Quem autoriza?
- Como fecha?
- Dinheiro é contado por quem?
- Pix/cartão são reconciliados como?
- O operador enxerga o valor esperado antes de contar?
- O que acontece se houver diferença?
- Há tolerância?
- Gerente precisa aprovar?
- Como o fechamento é guardado hoje?

Perguntar se fechamento cego seria desejável.

---

# 11. Funcionários e permissões

Para cada perfil:

- proprietário;
- gerente;
- vendedor;
- caixa;
- estoque;
- contador.

Perguntar:

- o que pode ver;
- o que pode criar;
- o que pode alterar;
- o que pode cancelar;
- quais valores comerciais são confidenciais;
- quem vê custo/margem;
- quem vê vendas de outras pessoas;
- quem vê comissão;
- quem gerencia integrações/usuários.

Nunca assumir que todo funcionário deve ter a mesma tela.

---

# 12. Comissão

Esta é uma área que **não deve ser inventada pelo software**.

- Existe comissão hoje?
- Percentual é igual para todos?
- É sobre valor bruto ou líquido?
- Desconto reduz comissão?
- Promoção tem regra diferente?
- Algumas categorias têm percentual diferente?
- Devolução retira comissão?
- Troca como funciona?
- Cancelamento como funciona?
- Comissão é fechada em qual dia?
- Quem confere?
- Como é paga?
- Funcionária vê valor acumulado durante o mês?
- Existe meta/bonificação?
- Venda online gera comissão de alguém?

Levar política final ao contador/assessoria responsável quando aplicável.

---

# 13. Trocas, devoluções e cancelamentos

Registrar cenários reais:

- troca por mesmo valor;
- troca por valor maior;
- troca por valor menor;
- devolução integral;
- devolução parcial;
- cancelamento no mesmo dia;
- cancelamento após fechamento;
- produto com defeito;
- vale/crédito da loja, se existir.

Para cada caso: estoque, pagamento, fiscal, caixa e comissão precisam ficar consistentes.

---

# 14. Estoque e inventário

- Com que frequência fazem contagem?
- Contam a loja toda ou setores?
- Como registram divergência?
- Quem pode ajustar?
- É possível saldo negativo hoje?
- Como perdas/furtos/avarias são registrados?
- Existe reserva de produto para cliente?
- Há mercadoria consignada?
- Há produtos separados para outro canal?

## Transferência

- Como peça passa de uma loja para outra?
- Precisa registrar envio/recebimento ou basta transferência imediata?
- Quem autoriza?

---

# 15. Compras e fornecedores

- Como decide o que comprar?
- Há histórico por fornecedor?
- Usa curva ABC/giro?
- Há pedido de compra?
- Há prazo de entrega?
- Há condição de pagamento?
- Como custo e margem são acompanhados?
- Precisa comparar pedido x recebido x nota?
- Produtos encalhados influenciam próxima compra?

---

# 16. Relatórios e decisões

Perguntar quais perguntas os proprietários realmente querem responder:

- Quanto vendeu hoje?
- Qual loja vendeu mais?
- Quais produtos vendem mais?
- Quais estão parados?
- Qual ticket médio?
- Quantas peças?
- Quais tamanhos/cores têm melhor giro?
- Qual fornecedor performa melhor?
- Quanto há de estoque a custo/preço?
- Quais itens estão acabando?
- Quanto cada funcionária vendeu?
- Quanto é a comissão?
- Qual meio de pagamento predomina?
- Qual canal online é rentável?
- Qual margem real por canal após taxas/frete?

Classificar cada relatório como diário, semanal, mensal ou eventual.

---

# 17. Internet e contingência

- Internet costuma cair?
- Há internet reserva/4G/5G?
- O que precisa continuar funcionando obrigatoriamente sem internet?
- Quanto tempo uma indisponibilidade é aceitável?
- Se o sistema ficar indisponível, qual procedimento atual?

Distinguir:

- inventário offline;
- consulta offline;
- PDV offline;
- emissão fiscal em contingência.

São problemas diferentes.

---

# 18. Hardware

Inventariar:

```text
Computadores:
Sistema operacional:
Leitores:
Impressoras fiscais/não fiscais:
Impressora de etiquetas:
Maquininhas/TEF:
Smart POS:
Celulares/tablets:
Rede/Wi-Fi:
Nobreak:
Outros:
```

Fotografar etiquetas e periféricos quando útil.

---

# 19. Fiscal e contador

Este bloco deve ser validado com contador/profissional competente.

- NFC-e, NF-e e outros documentos usados hoje;
- certificado digital;
- CSC quando aplicável;
- séries/numeração;
- cancelamento;
- contingência;
- exportação XML;
- integração do contador;
- CNPJ por loja;
- regras para vendas online/marketplace;
- devoluções/trocas;
- mudanças tributárias relevantes.

Não substituir o componente fiscal atual antes de existir plano de contingência testado.

---

# 20. Marketplaces e venda online

Para TikTok Shop, Mercado Livre, Shopee e futuros canais:

- já existe conta?
- quem administra?
- quais produtos seriam vendidos?
- preço é igual em todos?
- estoque deve ser compartilhado?
- há estoque de segurança?
- quem separa pedidos?
- quem imprime etiqueta de envio?
- como será faturamento/nota?
- quem lida com devolução?
- como taxas/frete entram na margem?

Começar por poucos canais bem suportados.

---

# 21. SaaS futuro

Perguntas para testar a generalização:

- Que partes da operação Mora são universais?
- Que partes são específicas da família?
- O que precisa ser configurável?
- O que um novo lojista precisaria importar?
- Quanto tempo aceitável para onboarding?
- Que suporte ele precisaria?
- Um cliente sem site valorizaria criar um automaticamente?
- Quais limites poderiam diferenciar planos?
- Quais funções têm custo variável, como IA/storage?

Evitar `if organization == Mora` no produto.

---

# 22. Priorização por dor

Após entrevistas, preencher:

| Problema | Frequência | Impacto | Risco | Trabalho manual atual | Prioridade |
| --- | --- | --- | --- | --- | --- |
| Exemplo | diária | alto | médio | 20 min/dia | alta |

Prioridade vem de valor/risco/evidência, não do tamanho da lista de features.

---

# 23. Critério para encerrar Discovery inicial

Antes da Fase 1, devemos conseguir responder pelo menos:

- estrutura Organization/LegalEntity/Store/StockLocation;
- usuários/papéis principais;
- catálogo e variantes reais;
- fluxo de recebimento;
- estoque/ajustes/transferências;
- Bling atual;
- venda física;
- caixa;
- comissão;
- troca/devolução;
- meios de pagamento;
- fiscal atual;
- hardware;
- conectividade;
- dados a migrar;
- sites/canais desejados.

Questões sem resposta permanecem explicitamente abertas; não são preenchidas por suposição.

## Saídas do Discovery

O resultado deve produzir:

1. mapa do processo atual (`as-is`);
2. problemas/dor quantificados quando possível;
3. processo desejado (`to-be`);
4. regras e invariantes;
5. matriz de permissões;
6. exemplos reais de produto/variante;
7. mapa fiscal/pagamentos;
8. inventário de hardware;
9. requisitos de migração;
10. backlog priorizado;
11. ADRs iniciais;
12. critérios de aceitação da Fase 1.

## Documentos relacionados

- [Visão Executiva](../product/executive-overview.md)
- [Modelo de domínio](../domain/domain-model.md)
- [Vendas, caixa e comissões](../domain/sales-cash-commissions.md)
- [Benchmark competitivo](../research/competitive-benchmark.md)
- [Roadmap](../roadmap/roadmap.md)
