# Como rodar

O FinSight é um front estático, feito com HTML, CSS e JavaScript, que conversa com o Supabase. Não tem etapa de build. Você só precisa servir os arquivos e ter um banco Supabase configurado.

## O que você precisa antes

* Um navegador atual.
* Uma forma de servir arquivos por HTTP, que pode ser qualquer servidor estático. Abrir o HTML direto pelo caminho do arquivo não é recomendado, porque o app faz requisição de rede e usa caminhos relativos.
* Uma conta no [Supabase](https://supabase.com), com um projeto criado.

## 1. Clonar o projeto

```bash
git clone <url-do-repositorio>
cd finsight
```

## 2. Criar o banco no Supabase

1. No painel do Supabase, crie um projeto.
2. Abra o SQL Editor e rode o conteúdo de `supabase_schema.sql`. Isso cria as tabelas `cliente`, `conta`, `tipo`, `categoria` e `transacao`. Veja [Banco de dados](wiki-banco-de-dados.md).

## 3. Configurar as credenciais

As funções de acesso ao banco ficam em `js/supabase.js`, que inicia o cliente Supabase. O arquivo já vem com um `SUPABASE_URL` e uma `SUPABASE_KEY`, que é a chave anon, preenchidos lá no topo. Pra apontar pra o seu próprio projeto, troque esses dois valores pelos do seu Supabase. Você acha os dois em Project Settings e depois API, no painel.

## 4. Servir os arquivos

Suba a pasta do projeto com qualquer servidor estático. Algumas opções.

```bash
# Python, que já vem instalado em muitos sistemas
python -m http.server 5500

# Node, sem instalar nada permanente
npx serve

# No VS Code, a extensão Live Server, pelo botão Go Live.
# O projeto traz um .vscode/settings.json com a porta 5501 do Live Server.
```

## 5. Abrir no navegador

Acesse o endereço que está sendo servido, por exemplo `http://localhost:5500`. O `index.html` redireciona sozinho pra a tela de login.

## 6. Usar o app

1. Em Criar conta, cadastre um usuário.
2. Faça login. A sessão fica guardada no `localStorage`.
3. Crie uma carteira na tela de Carteiras. O app precisa de pelo menos uma conta pra você conseguir lançar transações.
4. Crie algumas categorias.
5. Comece a registrar receitas e despesas pelo botão flutuante de mais.

A partir daí, o dashboard, as metas e os relatórios passam a refletir os seus dados.

Uma dica. Pra zerar o estado local do navegador, como sessão, tema e PIN, é só limpar o `localStorage` do site nas ferramentas de desenvolvedor. Você também pode usar o botão Sair da conta no perfil, que faz isso pra você.
