# Arquitetura

O FinSight é uma aplicação front-end multipágina (MPA): cada tela é um arquivo HTML próprio, e os arquivos compartilham CSS e scripts. Não há build — o navegador carrega os arquivos como estão.

## Estrutura de pastas

```
finsight/
├── index.html              # Porta de entrada — redireciona para o login
├── supabase_schema.sql     # Schema do banco (Postgres / Supabase)
├── css/
│   ├── global.css          # Design system: variáveis, reset, layout, componentes
│   └── modals.css          # Sistema de modais (bottom sheets)
├── js/
│   ├── supabase.js         # Cliente Supabase + funções de acesso ao banco (db*)
│   ├── modals.js           # Comportamentos globais: modais, tema, sidebar, etc.
│   └── app.js              # Script carregado em todas as páginas internas
└── pages/
    ├── login.html          # Entrar
    ├── register.html       # Criar conta
    ├── dashboard.html      # Visão geral
    ├── transactions.html   # Atividade (lista de transações)
    ├── wallets.html        # Carteiras
    ├── goals.html          # Metas
    ├── categories.html     # Categorias
    ├── reports.html        # Relatórios
    └── profile.html        # Perfil e segurança
```

### O que cada parte faz

- **`index.html`** — documento mínimo que só redireciona para `pages/login.html` (via `meta refresh` e `window.location.replace`).
- **`css/global.css`** — o coração do visual. Define as variáveis de cor/tipografia/raio/sombra no `:root`, o reset, e os componentes (appbar, cards, botões, listas de transação, gráficos, navegação, etc.). Também tem o tema claro em `html:not(.dark)`.
- **`css/modals.css`** — todo o sistema de modais, que aparecem como *bottom sheets* (deslizam de baixo para cima) com overlay e blur.
- **`js/supabase.js`** — inicializa o cliente Supabase e concentra o acesso a dados em funções `db*`. As páginas chamam essas funções; não falam SQL diretamente.
- **`js/modals.js`** — comportamentos que valem para o app inteiro (detalhado abaixo).
- **`js/app.js`** — script presente em todas as páginas internas (carregado junto de `supabase.js` e `modals.js`).
- **`pages/`** — uma página por tela. Cada uma carrega o CSS, os scripts compartilhados e tem, ao final, um `<script>` com a lógica específica daquela tela.

## Como a navegação funciona

A navegação é por **links comuns entre arquivos HTML** — cada clique carrega uma página nova. Não há roteador nem SPA.

- **`index.html`** manda direto para o login.
- O **login** guarda `finsight-session = 'true'` no `localStorage` e vai para o dashboard.
- Toda página interna começa com uma **trava de sessão**: se `finsight-session` não for `'true'`, redireciona de volta para o login. As telas de login/registro fazem o inverso — se já há sessão, pulam para o dashboard.
- A **navegação entre telas** acontece por dois componentes:
  - **Bottom nav** (barra inferior) no mobile, com Dashboard, Atividade, Carteiras, Metas e Perfil.
  - **Sidebar** no desktop (≥ 1024px), montada por JavaScript (`buildSidebarHTML` / `setupDesktopSidebar` em `modals.js`), que injeta um `<aside>` com os links de navegação, incluindo também Categorias e Relatórios.
- O item ativo da navegação é marcado comparando a URL atual com o `href` de cada link.

## Padrões identificados no código

### Design system via variáveis CSS

Cores, tipografia, raios de borda, sombras e medidas de layout ficam em Custom Properties no `:root` de `global.css`. Exemplos:

```css
:root {
  --accent: #7c5cfc;
  --income: #22c55e;
  --expense: #ef4444;
  --sans: 'Public Sans', sans-serif;
  --mono: 'JetBrains Mono', monospace;
  --r-md: 12px;
  --sidebar-w: 260px;
}
```

O **tema claro/escuro** é só uma troca desses valores: o tema claro é definido em `html:not(.dark)`, e alternar tema é adicionar/remover a classe `dark` no `<html>`.

### Comportamento por data attributes

Em vez de amarrar JavaScript a IDs específicos, `modals.js` varre o DOM por atributos e liga os comportamentos. Assim, qualquer página ganha o recurso só usando o atributo certo:

| Atributo | O que faz |
|----------|-----------|
| `data-modal="id"` | Abre o modal de id correspondente |
| `data-close="id"` | Fecha o modal |
| `data-pw-toggle="id"` | Mostra/oculta o campo de senha |
| `data-tabs` / `data-tab` | Grupo de abas e cada aba |
| `data-toggle-parcel` | Mostra/oculta o campo de parcelas |
| `data-toggle-fixed` | Mostra/oculta o campo de despesa fixa |
| `data-target` | Card do FAB que aponta para um modal específico |

### Modais como bottom sheets

Todos os modais seguem a mesma marcação (`.modal-overlay` > `.modal-sheet` com `.modal-handle`, `.modal-header`, `.modal-body`). Abrir/fechar é só alternar a classe `.is-open`. Dá para fechar clicando no overlay ou apertando `Esc`. Quando um modal está aberto, o `<body>` ganha `modal-open`.

### Lógica compartilhada vs. lógica de página

`modals.js` cuida do que é transversal: tema, sidebar do desktop, dados do usuário na appbar, seletores de ícone/cor, força de senha, datas padrão para hoje, etc. Já a lógica específica de cada tela (carregar dados, renderizar listas, salvar formulários) fica num `<script>` no fim do próprio HTML, sempre chamando as funções `db*` de `supabase.js`.

### Dados ricos guardados como JSON

Dois pontos do app guardam um objeto JSON dentro de um campo de texto do banco, para carregar mais informação do que o schema prevê:

- **Categorias**: o campo `nome_categoria` guarda `{ nome, icone, cor, tipo }`.
- **Metas**: o campo `nome` (da tabela `tipo`) guarda `{ nome, icone }`.

Por isso o código tem helpers `parseCat()` / `parseMeta()` que fazem o `JSON.parse` com um fallback caso o valor seja texto puro.

### Gráficos desenhados na mão

Os gráficos não usam biblioteca: o donut de gastos por categoria é montado gerando paths SVG na hora (no dashboard), e os gráficos de barras (balanço de 6 meses, evolução nos relatórios) são `div`s com altura proporcional ao valor.
