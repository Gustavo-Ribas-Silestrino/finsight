# Como rodar

O FinSight é um front-end estático (HTML, CSS e JS) que conversa com o Supabase. Não tem etapa de build — você só precisa servir os arquivos e ter um banco Supabase configurado.

## Pré-requisitos

- Um navegador atual.
- Uma forma de servir arquivos por HTTP (qualquer servidor estático serve). Abrir o HTML com `file://` direto não é recomendado, porque o app faz requisições à rede e usa caminhos relativos.
- Uma conta no [Supabase](https://supabase.com) com um projeto criado.

## 1. Clonar o projeto

```bash
git clone <url-do-repositorio>
cd finsight
```

## 2. Criar o banco no Supabase

1. No painel do Supabase, crie um projeto.
2. Abra o **SQL Editor** e rode o conteúdo de `supabase_schema.sql`. Isso cria as tabelas `cliente`, `conta`, `tipo`, `categoria` e `transacao` (ver [Banco de dados](wiki-banco-de-dados.md)).

## 3. Configurar as credenciais

As funções de acesso ao banco ficam em `js/supabase.js`, que inicializa o cliente Supabase. O arquivo já vem com um `SUPABASE_URL` e uma `SUPABASE_KEY` (chave anon) preenchidos no topo. Para apontar para o **seu** projeto, troque esses dois valores pelos do seu Supabase (encontrados em *Project Settings → API* no painel).

## 4. Servir os arquivos

Suba a pasta do projeto com qualquer servidor estático. Algumas opções:

```bash
# Python (já vem instalado em muitos sistemas)
python -m http.server 5500

# Node, sem instalar nada permanentemente
npx serve

# VS Code: extensão "Live Server" → botão "Go Live"
# (o projeto traz .vscode/settings.json com a porta 5501 do Live Server)
```

## 5. Abrir no navegador

Acesse o endereço servido (por exemplo `http://localhost:5500`). O `index.html` redireciona automaticamente para a tela de login.

## 6. Usar o app

1. Em **Criar conta**, cadastre um usuário.
2. Faça **login** — a sessão fica guardada no `localStorage`.
3. Crie uma **carteira** em Carteiras (o app precisa de ao menos uma conta para lançar transações).
4. Crie algumas **categorias**.
5. Comece a registrar **receitas e despesas** pelo botão flutuante (+).

A partir daí, o dashboard, as metas e os relatórios passam a refletir os seus dados.

> Dica: para "resetar" o estado local do navegador (sessão, tema e PIN), é só limpar o `localStorage` do site nas ferramentas de desenvolvedor — ou usar o botão "Sair da conta" no perfil, que faz isso por você.
