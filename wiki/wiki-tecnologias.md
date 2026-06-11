# Tecnologias

A stack do FinSight é enxuta de propósito. Front feito com HTML, CSS e JavaScript puro, conversando direto com o Supabase. Não tem etapa de build, nem bundler, nem framework no meio do caminho.

## HTML, CSS e JavaScript puro, sem framework

Todo o app é feito com as três tecnologias base da web.

* **HTML5.** Uma página por tela, dentro de `pages/`. Cada página é um documento completo que carrega os scripts que precisa.
* **CSS.** São dois arquivos. O `css/global.css` é o design system, com variáveis, reset, layout e componentes. O `css/modals.css` cuida de todo o sistema de modais. O visual é controlado por variáveis CSS, o que faz o tema claro e escuro virar só uma troca de valores.
* **JavaScript puro.** Sem framework. A lógica fica em scripts compartilhados na pasta `js/` e em blocos de script no fim de cada página. O código usa `'use strict'`, mexe no DOM direto e organiza comportamentos através de atributos de dado, como `data-modal`, `data-close` e `data-tab`.

Por que sem framework? Pra um projeto desse tamanho, o JavaScript puro resolve sem peso extra. Não precisa de build, dá pra abrir os arquivos e entender o que cada um faz, e o carregamento é mínimo, já que você baixa só o que a página usa. Como o app gira em torno de formulários, listas e alguns gráficos simples desenhados na mão em SVG, não existe uma complexidade de estado que justifique trazer React, Vue ou parecidos. É uma escolha que mantém o projeto leve e fácil de estudar, o que faz todo sentido num contexto de aprendizado.

## Supabase

O backend é o Supabase, que entra como banco de dados Postgres e camada de acesso a dados. O cliente JavaScript é carregado por CDN em todas as páginas.

```html
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2/dist/umd/supabase.js"></script>
```

A inicialização do cliente e todas as funções de acesso ficam em `js/supabase.js`. O arquivo cria o cliente com `createClient(SUPABASE_URL, SUPABASE_KEY)` e expõe funções como `dbLogin`, `dbRegister`, `dbGetContas`, `dbGetTodasTransacoes`, `dbGetCategorias`, `dbGetMetas`, `dbAddTransacao`, `dbUpdateSaldo`, `dbAddContaComSaldo`, `dbDeleteConta`, `dbAddMeta`, `dbAporteMeta` e outras. As páginas chamam essas funções em vez de falar com o Supabase direto. A tela de perfil também usa o cliente `_db` diretamente pra atualizar a tabela `cliente`.

As consultas usam o recurso de junção do Supabase pra trazer dados relacionados de uma vez só. Um exemplo é a `conta` já vir com os seus `tipo`, ou a `transacao` vir com `categoria` e `tipo` e a `conta` aninhados. Isso deixa o render das telas mais rápido, com menos idas ao banco.

O schema do banco está versionado em `supabase_schema.sql`, detalhado em [Banco de dados](wiki-banco-de-dados.md).

## Recursos via CDN

Além do Supabase, o projeto puxa de CDN as fontes e os ícones do Google.

* **Public Sans.** A fonte principal, sem serifa.
* **JetBrains Mono.** Usada nos valores em dinheiro, pela classe `.mono`.
* **Material Symbols Outlined.** Toda a iconografia do app.

```html
<link href="https://fonts.googleapis.com/css2?family=Public+Sans:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
<link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:wght,FILL@100..700,0..1&display=swap" rel="stylesheet">
```

## Armazenamento no navegador

O app usa o `localStorage` pra guardar estado entre as páginas.

* **`finsight-session`.** Marca se existe sessão ativa, com o valor `'true'`.
* **`finsight-user`.** Os dados do usuário logado, em JSON.
* **`finsight-theme`.** A preferência de tema, sendo `'dark'` ou `'light'`.
* **`finsight-pin`.** O PIN de quatro dígitos da área de segurança.

## Resumo da stack

* **Estrutura.** HTML5.
* **Estilo.** CSS com variáveis e design system.
* **Lógica.** JavaScript puro, sem framework.
* **Banco e backend.** Supabase, sobre Postgres, pelo `@supabase/supabase-js` na versão 2.
* **Fontes e ícones.** Google Fonts, com Public Sans, JetBrains Mono e Material Symbols.
* **Estado no cliente.** `localStorage`.
