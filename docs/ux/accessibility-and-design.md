# UX, Design System e Acessibilidade

> **Status:** princípios de experiência. Design system visual final será definido quando as aplicações forem implementadas.

## Objetivo

Fazer o Mora Core parecer uma ferramenta de varejo rápida, previsível e confiável — não um template genérico de SaaS cheio de dashboards e menus irrelevantes.

## Princípios

1. tarefa principal visível;
2. progressive disclosure;
3. feedback imediato;
4. erros acionáveis;
5. contexto de organização/loja claro;
6. autorização refletida na UI, mas garantida no servidor;
7. acessibilidade desde o componente;
8. mobile operacional pensado para uso real;
9. ação destrutiva tem confirmação proporcional;
10. números críticos mostram origem/status quando necessário.

---

# Design por perfil

## Vendedor

Interface enxuta:

- venda;
- produto/estoque;
- próprias métricas/comissão quando permitidas.

Não mostrar configuração de integração, custo ou administração.

## Estoque

Priorizar scanner, recebimento, contagem e transferência.

## Gerente

Pendências/aprovações e visão da loja.

## Proprietário

Visão consolidada e drill-down.

## Administrador SaaS

Ferramentas de plataforma separadas da experiência do lojista.

---

# Contexto visível

Usuário precisa saber onde está operando:

```text
Organização: Mora
Loja: Feminina
```

Mudança de loja/tenant deve ser deliberada e clara. Cor/ícone não são únicos indicadores.

Ações como ajuste/caixa exibem contexto antes de confirmar.

---

# Mobile operacional

Uso real pode ocorrer:

- em pé;
- com uma mão;
- em estoque apertado;
- iluminação ruim;
- câmera ativa;
- internet instável.

Requisitos:

- touch targets adequados;
- botões principais alcançáveis;
- scanner com feedback sonoro/háptico configurável;
- último item visível;
- contagem grande/legível;
- não exigir digitação repetitiva;
- preservar progresso;
- erro de rede não parecer sucesso.

## Scanner

Modo coletor deve minimizar navegação:

```text
scan
→ identificação
→ feedback
→ +1
→ pronto para próximo
```

Duplicidade intencional de scan (quantidade) precisa ser diferenciada de leitura acidental repetida conforme UX testada.

---

# Formulários

- labels persistentes;
- exemplos/formatos;
- erros junto ao campo e resumo quando necessário;
- não apagar dados após erro;
- defaults seguros;
- autocomplete onde apropriado;
- máscara não deve impedir paste/accessibility;
- salvar draft em fluxos longos.

## Validação

Cliente dá feedback rápido; servidor continua autoridade.

Erro de negócio:

> “Essa combinação Azul/G não existe para este produto.”

é melhor que:

> “422 Unprocessable Entity”.

---

# Estados de interface

Toda tela assíncrona considera:

- loading;
- empty;
- error;
- stale/offline;
- success;
- partial success;
- permission denied.

Não usar spinner infinito.

## Jobs

Publicação/import/IA mostram:

```text
QUEUED
PROCESSING
READY
FAILED — motivo + ação
```

---

# Confirmação e undo

## Ações reversíveis

Preferir undo/soft action quando seguro.

## Ações irreversíveis ou financeiras

Confirmar com contexto:

```text
Cancelar venda #123 de R$ 1.500?
Isso gerará reversões de estoque/comissão/pagamento conforme regra.
```

Step-up/approval não deve ser substituído por modal “Tem certeza?”.

---

# Feedback de dinheiro/estoque

Valores usam formatação consistente de moeda. Nunca floating artifacts.

Disponibilidade pode usar:

- Em estoque;
- Últimas unidades;
- Indisponível;
- Consultar disponibilidade.

Quantidade exata apenas quando policy permitir.

---

# Dashboards

Priorizar decisão, não vanity metrics.

Cada card responde:

- o que é?
- período?
- comparação com quê?
- posso abrir detalhe?
- existe ação?

Não usar seta verde/vermelha sem definição de baseline.

---

# Acessibilidade web — baseline WCAG

Alvo: seguir WCAG 2.2 AA quando aplicável, validado com testes reais, sem claim de conformidade apenas por checklist.

## Semântica

- headings hierárquicos;
- landmarks;
- button é button;
- link é link;
- tabelas com headers;
- listas semânticas.

## Teclado

Toda ação web importante é operável sem mouse:

- foco visível;
- ordem lógica;
- sem keyboard trap;
- Escape em modal/viewer;
- restore focus;
- menus navegáveis.

## Formulários

- label programático;
- `aria-describedby` para ajuda/erro quando apropriado;
- erro não depende só de cor;
- required comunicado;
- autocomplete tokens apropriados.

## Contraste

Tokens de design devem cumprir contraste. Estados disabled/focus/error também.

## Zoom

Não bloquear browser zoom. Layout suporta 200%/reflow conforme aplicável.

## Motion

Respeitar `prefers-reduced-motion`; animação não é necessária para compreender estado.

## Touch

Targets e espaçamento adequados; evitar gesto como única forma de ação.

---

# Screen reader

Testar jornadas críticas com pelo menos leitor de tela representativo por plataforma quando UI madura:

- login;
- produto;
- venda/admin;
- modal;
- tabela;
- storefront checkout/WhatsApp CTA.

Automated axe/Lighthouse ajuda, mas não substitui teste humano.

---

# Câmera e acessibilidade

Scanner precisa de fallback:

- digitar código;
- pesquisar nome/SKU.

Se câmera negada, explicar como habilitar ou continuar manualmente.

Não exigir percepção visual de bounding box para concluir tarefa.

---

# Imagens

## Storefront

Alt text descreve produto de forma útil, sem stuffing de SEO.

Imagem decorativa usa alt vazio.

IA pode sugerir alt, mas revisão/grounding evita inventar características.

---

# Tabelas e grandes listas

Admin de estoque/vendas precisa:

- sort/filter acessível;
- paginação/virtualização sem quebrar teclado/leitor;
- cabeçalho claro;
- densidade configurável quando útil;
- ações por linha não escondidas apenas no hover.

---

# Cores/status

Nunca comunicar estado apenas por vermelho/verde.

Usar ícone/texto:

```text
✓ Publicado
⚠ Atenção
✕ Falhou
```

---

# Design system

## Tokens

- color;
- typography;
- spacing;
- radius;
- elevation;
- motion;
- breakpoints.

## Componentes

- Button;
- Input;
- Select/Combobox;
- Dialog;
- Toast/Inline alert;
- Table;
- Card;
- Tabs;
- Navigation;
- Product image viewer;
- Scanner surface;
- Status badge.

Cada componente inclui estados de teclado, erro, loading e disabled.

## Storefront themes

Brand theme usa tokens/presets seguros, separado do design system do admin.

---

# Responsividade

Testar pelo menos:

- 320;
- 360;
- 390;
- 430;
- tablet;
- desktop;
- 1440+ quando relevante.

Não “encolher desktop” para mobile.

---

# Performance percebida

- optimistic UI somente quando rollback é seguro;
- skeleton apenas se ajuda compreensão;
- não bloquear tela por job assíncrono;
- feedback de scan imediato;
- upload mostra progresso/estado.

Nunca mostrar sucesso local antes de saber se operação financeira crítica foi aceita, salvo modo offline explicitamente marcado.

---

# Conteúdo e linguagem

Usar linguagem do comércio:

- Sangria;
- Suprimento;
- Estoque;
- Vendedora;
- Comissão;
- Troca;

Termos técnicos como PIM/OMS ficam em docs/config avançada, não na tela cotidiana se usuário não precisa deles.

## Microcopy de erro

Bom:

> “Não foi possível sincronizar com o Mercado Livre. O produto continua salvo no Mora Core. Tentar novamente.”

Ruim:

> “Error 500”.

---

# Segurança na UX

- password manager-friendly;
- MFA claro;
- sessões/dispositivos;
- não expor secret;
- confirmação de tenant/loja;
- approvals identificam aprovador;
- não induzir compartilhamento de login.

---

# Testes de usabilidade

Observar pessoas reais executando:

- receber caixa de mercadoria;
- scan;
- criar grade;
- venda;
- fechamento;
- inventário;
- publicar produto.

Medir:

- tempo;
- erros;
- hesitação;
- ajuda necessária;
- retrabalho.

Não perguntar apenas “gostou?”.

---

# Critérios de aceite UX

Feature operacional não está pronta se:

- só funciona com mouse quando deveria ser acessível;
- erro perde formulário;
- contexto da loja é ambíguo;
- estado offline parece online;
- ação crítica sem feedback;
- mobile exige scroll/precision excessivos;
- contraste/foco falham;
- câmera sem fallback.

## Relacionados

- [Personas](../product/personas-and-journeys.md)
- [Mobile](../mobile/mobile-app.md)
- [Storefront](../product/storefront.md)
- [QA](../qa/test-strategy.md)
