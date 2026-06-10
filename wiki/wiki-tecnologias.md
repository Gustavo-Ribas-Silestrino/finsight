# Tecnologias

A stack do FinSight é enxuta de propósito: front-end em HTML, CSS e JavaScript puro, conversando direto com o Supabase. Não há build, bundler nem framework no meio do caminho.

## HTML, CSS e JavaScript puro (sem framework)

Todo o app é feito com as três tecnologias base da web:

- **HTML5** — uma página por tela, dentro de `pages/`. Cada página é um documento completo que carrega os scripts de que precisa.
- **CSS** — dois arquivos: `css/global.css` (o design system: variáveis, reset, layout, componentes) e `css/modals.css` (todo o sistema de modais). O visual é controlado por **CSS Custom Properties** (variáveis), o que torna o tema claro/escuro só uma questão de trocar valores.
- **JavaScript "vanilla"** — sem framework. A lógica fica em scripts `js/` compartilhados e em blocos `<script>` no fim de cada página. O código usa `'use strict'`, manipula o DOM diretamente e organiza comportamentos por meio de **data attributes** (ex.: `data-modal`, `data-close`, `data-tab`).

**Por que sem framework?** Para um projeto desse porte, vanilla JS resolve sem peso extra. Não precisa de etapa de build, dá para abrir os arquivos e entender o que cada um faz, e o tempo de carregamento é mínimo — você baixa só o que a página usa. Como o app é centrado em formulários, listas e alguns gráficos simples (desenhados na mão em SVG), não há complexidade de estado que justifique trazer React, Vue ou similar. É uma escolha que mantém o projeto leve e fácil de estudar — o que faz sentido num contexto de aprendizado.

## Supabase

O back-end é o **Supabase**, que entra como banco de dados (Postgres) e camada de acesso a dados. O cliente JavaScript é carregado por CDN em todas as páginas:

```html
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2/dist/umd/supabase.js"></script>
```

A inicialização do cliente e todas as funções de acesso ficam em `js/supabase.js`. O arquivo cria o cliente com `createClient(SUPABASE_URL, SUPABASE_KEY)` — a URL do projeto e a **chave anon ficam escritas direto no arquivo** — e expõe funções como `dbLogin`, `dbRegister`, `dbGetContas`, `dbGetTodasTransacoes`, `dbGetCategorias`, `dbGetMetas`, `dbAddTransacao`, `dbUpdateSaldo`, `dbAddContaComSaldo`, `dbDeleteConta`, `dbAddMeta`, `dbAporteMeta` e outras. As páginas chamam essas funções em vez de falar com o Supabase diretamente — exceto a tela de perfil, que usa o cliente `_db` direto para atualizar a tabela `cliente`.

As queries usam o recurso de *joins* do Supabase para trazer dados relacionados de uma vez (ex.: `conta` já com seus `tipo`, ou `transacao` com `categoria` e `tipo(conta)` aninhados). O login compara e-mail e senha direto na tabela `cliente` — vale saber que **a senha trafega e é comparada em texto puro**, sem hash; é uma simplificação adequada ao contexto escolar do projeto, não algo para produção.

O schema do banco está versionado em `supabase_schema.sql` (detalhado em [Banco de dados](wiki-banco-de-dados.md)).

## Recursos via CDN

Além do Supabase, o projeto puxa de CDN:

- **Google Fonts**:
  - **Public Sans** — fonte principal (sans-serif).
  - **JetBrains Mono** — usada nos valores monetários (classe `.mono`).
  - **Material Symbols Outlined** — toda a iconografia do app.

```html
<link href="https://fonts.googleapis.com/css2?family=Public+Sans:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
<link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:wght,FILL@100..700,0..1&display=swap" rel="stylesheet">
```

## Armazenamento no navegador

O app usa o **`localStorage`** para guardar estado entre páginas:

| Chave | Para que serve |
|-------|----------------|
| `finsight-session` | Marca se há sessão ativa (`'true'`) |
| `finsight-user` | Dados do usuário logado (JSON) |
| `finsight-theme` | Preferência de tema (`'dark'` / `'light'`) |
| `finsight-pin` | PIN de 4 dígitos da área de segurança |

## Resumo da stack

| Camada | Tecnologia |
|--------|------------|
| Estrutura | HTML5 |
| Estilo | CSS3 (variáveis / design system) |
| Lógica | JavaScript puro (ES, sem framework) |
| Banco / back-end | Supabase (Postgres) via `@supabase/supabase-js` v2 |
| Fontes e ícones | Google Fonts: Public Sans, JetBrains Mono, Material Symbols |
| Estado no cliente | `localStorage` |
