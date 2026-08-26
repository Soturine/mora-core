# Naming, Marca, Propriedade e Licenciamento

> **Status:** “Mora Core” é o nome oficial de trabalho do projeto/produto. Titularidade societária, registro de marca e estratégia final de visibilidade ainda precisam de decisões jurídicas/comerciais.

## Nome principal

**Mora Core**

Racional:

- “Core” representa o núcleo que conecta catálogo, estoque, vendas, equipe, caixa e canais;
- não limita o produto a ERP, PDV, estoque ou moda;
- continua fazendo sentido se evoluir para SaaS, mobile, sites e omnichannel.

## Nomes de componentes

Convenções possíveis:

```text
Mora Core              plataforma/produto
Mora Core Admin        painel web
Mora Core Mobile       aplicativo
Mora Core POS          PDV
Mora Storefront        engine de sites
AI Studio              área/capability de IA dentro do Core
Mora Connect           nome comercial futuro para integrações, somente se fizer sentido
```

Não criar sub-marcas antes da necessidade; nomes de módulos internos podem permanecer técnicos.

## Repositório

Nome principal:

```text
mora-core
```

O repositório é a fonte canônica de arquitetura/implementação da plataforma, enquanto os sites existentes podem permanecer em repos separados e consumir a API futuramente.

## Copyright

Texto atual:

> **© 2026 Mora Core. Todos os direitos reservados.**

Esse aviso comunica intenção de reserva de direitos, mas não substitui definição jurídica do titular.

## Titular legal

Antes de contratos/investimento/SaaS comercial, definir se o titular será:

- pessoa física autora;
- empresa futura;
- outra estrutura societária.

Quando houver razão social, atualizar LICENSE, contratos, termos e notices para o titular correto.

## Licença do repositório

Mora Core é **software proprietário**.

Não usar MIT, Apache, GPL etc. se a intenção é manter exploração comercial fechada.

Para package manifests futuros em ecossistema Node/TypeScript:

```json
{
  "private": true,
  "license": "UNLICENSED"
}
```

quando apropriado.

`UNLICENSED` não significa domínio público; sinaliza ausência de licença open source.

## Repo público vs software proprietário

Um repositório público continua protegido por copyright quando não há licença que conceda direitos amplos. Porém, **publicar o código expõe tecnicamente a implementação**, mesmo que copiar seja juridicamente proibido.

Antes de adicionar código comercial sensível, reavaliar:

- manter repo privado;
- deixar docs/case público separado;
- publicar apenas SDK/specs;
- estratégia de source-available/open-source, se algum dia houver motivo.

Não assumir que “All Rights Reserved” impede alguém de visualizar/clonar tecnicamente um repo público.

## Marca

Antes de investir em identidade visual, app stores, marketing ou contratos, fazer auditoria de naming:

- INPI;
- domínio `.com.br`;
- domínio `.com`;
- Google Play;
- App Store;
- GitHub;
- LinkedIn;
- Instagram;
- TikTok;
- softwares/SaaS com nome igual ou semelhante;
- classes de marca relevantes.

Depois avaliar pedido de registro de marca no INPI com estratégia adequada.

## Software no INPI

Registro de programa de computador pode ser considerado quando houver implementação suficientemente consolidada e fizer sentido como evidência de autoria/titularidade.

Não tratar registro como substituto de:

- Git history;
- contratos de cessão;
- controle de acesso;
- proteção de secrets;
- marca registrada.

## Contribuições futuras

Se terceiros contribuírem para software proprietário, definir previamente:

- vínculo/contrato;
- cessão/licença de direitos;
- confidencialidade;
- uso de dependências open source;
- provenance.

Não aceitar código copiado sem licença compatível.

## Dependências open source

Software proprietário pode usar open source, mas precisa respeitar licenças.

Supply-chain review inclui:

- licença;
- notices;
- copyleft obligations;
- distribuição;
- versão/provenance.

## Marca de clientes SaaS

Storefronts usam branding dos próprios lojistas. Mora Core deve definir termos futuros sobre:

- logo/marca enviada pelo cliente;
- domínio;
- conteúdo/fotos;
- responsabilidade por direitos de uso;
- remoção em offboarding.

## IA e direitos de conteúdo

Antes de comercializar geração de imagem/modelo virtual:

- avaliar termos dos providers;
- direitos de entrada/saída;
- uso de imagens de pessoas;
- disclosure por canal;
- propriedade/licença dos assets gerados.

## Claims

Não usar:

- “marca registrada”/`®` sem registro válido;
- “patenteado” sem patente;
- “LGPD compliant” sem base/evidência;
- “100% seguro”.

## Relacionados

- [LICENSE](../../LICENSE)
- [Projeto e Portfólio](project-presentation-and-portfolio.md)
- [Privacidade/LGPD](../security/privacy-lgpd.md)
