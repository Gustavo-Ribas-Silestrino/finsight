# Arquitetura

O FinSight é uma aplicação front de várias páginas. Cada tela é um arquivo HTML próprio, e os arquivos compartilham CSS e scripts. Não tem build. O navegador carrega os arquivos do jeito que eles estão.

## Estrutura de pastas

```
finsight/
├── index.html              Porta de entrada que redireciona pra o login
├── supabase_schema.sql     Schema do banco, em Postgres pelo Supabase
├── DOCS.md                 Documentação de apoio do projeto
├── INTEGRACAO.md           Notas sobre integração com o backend
├── .gitignore              Ignora a pasta .vercel
├── .vscode/
│   └── settings.json       Porta do Live Server, a 5501
├── css/
│   ├── global.css          Design system, com variáveis, reset, layout e componentes
│   └── modals.css          Sistema de modais, no formato de folha que sobe de baixo
├── js/
│   ├── supabase.js         Cliente Supabase e as funções de acesso ao banco
│   ├── modals.js           Comportamentos globais, como modais, tema e barra lateral
│   └── app.js              Funções utilitárias de apoio
└── pages/
    ├── login.html          Entrar
    ├── register.html       Criar conta
    ├── dashboard.html      Visão geral
    ├── transactions.html   Atividade, com a lista de transações
    ├── wallets.html        Carteiras
    ├── goals.html          Metas
    ├── categories.html     Categorias
    ├── reports.html        Relatórios
    └── profile.html        Perfil e segurança
```

## O que cada parte faz

* **`index.html`.** Documento mínimo que só redireciona pra `pages/login.html`, usando `meta refresh` e `window.location.replace`.
* **`css/global.css`.** É o coração do visual. Define as variáveis de cor, tipografia, raio e sombra no `:root`, o reset e os componentes, como a barra do topo, os cards, os botões, as listas de transação, os gráficos e a navegação. Também tem o tema claro, em `html:not(.dark)`.
* **`css/modals.css`.** Todo o sistema de modais, que aparecem como folhas que deslizam de baixo pra cima, com fundo escurecido e desfoque.
* **`js/supabase.js`.** Inicia o cliente Supabase e concentra o acesso a dados nas funções com prefixo `db`. As páginas chamam essas funções, sem escrever consulta direto.
* **`js/modals.js`.** Comportamentos que valem pra o app inteiro, detalhados mais abaixo.
* **`js/app.js`.** Funções utilitárias de apoio, carregado em todas as páginas internas.
* **`DOCS.md` e `INTEGRACAO.md`.** Documentos de apoio do projeto, com notas de documentação e de integração.
* **`pages/`.** Uma página por tela. Cada uma carrega o CSS, os scripts compartilhados e tem, no fim, um bloco de script com a lógica daquela tela.

## Como a navegação funciona

A navegação é por links comuns entre arquivos HTML. Cada clique carrega uma página nova. Não tem roteador, nem aplicação de página única.

* O `index.html` manda direto pra o login.
* O login guarda `finsight-session` como `'true'` no `localStorage` e vai pra o dashboard.
* Toda página interna começa com uma trava de sessão. Se `finsight-session` não for `'true'`, ela redireciona de volta pra o login. As telas de login e cadastro fazem o contrário. Se já existe sessão, elas pulam pra o dashboard.
* A navegação entre telas acontece por dois componentes. No celular tem a barra de baixo, com Dashboard, Atividade, Carteiras, Metas e Perfil. No desktop, a partir de 1024 pixels, aparece a barra lateral, montada por JavaScript em `modals.js`, que injeta um elemento `<aside>` com os links, incluindo também Categorias e Relatórios.
* O item ativo da navegação é marcado comparando a URL atual com o destino de cada link.

## Padrões que dá pra notar no código

### Design system por variáveis CSS

Cores, tipografia, raios de borda, sombras e medidas de layout ficam em variáveis no `:root` do `global.css`. Um exemplo.

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

O tema claro e escuro é só uma troca desses valores. O tema claro é definido em `html:not(.dark)`, e alternar o tema é adicionar ou tirar a classe `dark` do elemento `<html>`.

### Comportamento por atributos de dado

Em vez de amarrar JavaScript a IDs específicos, o `modals.js` varre o DOM atrás de atributos e liga os comportamentos. Assim, qualquer página ganha o recurso só usando o atributo certo.

* **`data-modal="id"`.** Abre o modal com o id correspondente.
* **`data-close="id"`.** Fecha o modal.
* **`data-pw-toggle="id"`.** Mostra ou esconde o campo de senha.
* **`data-tabs` e `data-tab`.** O grupo de abas e cada aba.
* **`data-toggle-parcel`.** Mostra ou esconde o campo de parcelas.
* **`data-toggle-fixed`.** Mostra ou esconde o campo de despesa fixa.
* **`data-target`.** O card do botão flutuante que aponta pra um modal específico.

### Modais como folhas que sobem

Todos os modais seguem a mesma marcação, com `.modal-overlay` por fora, `.modal-sheet` por dentro, e em volta um `.modal-handle`, um `.modal-header` e um `.modal-body`. Abrir ou fechar é só alternar a classe `.is-open`. Dá pra fechar clicando no fundo ou apertando a tecla Esc. Quando um modal está aberto, o `<body>` ganha a classe `modal-open`.

### Camada de acesso a dados centralizada

Nenhuma página escreve consulta do Supabase espalhada pelo código. Tudo passa pelo `js/supabase.js`, que expõe funções com prefixo `db` por entidade, cobrindo login, contas, tipos, metas, categorias e transações. As páginas chamam pelo nome. Dois detalhes do desenho dessa camada chamam atenção.

* **Junções aninhadas.** As leituras já trazem os relacionamentos prontos. A `dbGetContas` traz cada `conta` com os seus `tipo`. A `dbGetTodasTransacoes` traz cada `transacao` com a `categoria` e o `tipo` e a `conta` embutidos. Isso evita várias idas ao banco na hora de desenhar a tela.
* **Cascata feita na mão.** Como as exclusões precisam remover os filhos antes do pai, funções como `dbDeleteConta` e `dbDeleteMeta` apagam as transações, e os tipos, ligados ao registro antes de apagar o principal.

### Dados ricos guardados como JSON

Dois pontos do app guardam um objeto JSON dentro de um campo de texto do banco, pra carregar mais informação do que o schema prevê.

* **Categorias.** O campo `nome_categoria` guarda um objeto com nome, ícone, cor e tipo.
* **Metas.** O campo `nome` da tabela `tipo` guarda um objeto com nome e ícone.

Por isso o código tem ajudantes `parseCat()` e `parseMeta()`, que fazem o `JSON.parse` com um plano B caso o valor seja texto puro.

### Gráficos desenhados na mão

Os gráficos não usam biblioteca. A rosca de gastos por categoria é montada gerando caminhos SVG na hora, no dashboard. Os gráficos de barras, como o balanço de seis meses e a evolução nos relatórios, são elementos `div` com a altura proporcional ao valor.
