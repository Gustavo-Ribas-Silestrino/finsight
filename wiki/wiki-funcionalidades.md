# Funcionalidades

Cada arquivo dentro de `pages/` é uma tela do app. Abaixo está o que cada uma faz, seus principais elementos e como o usuário interage com ela.

## Login (pages/login.html)

É a porta de entrada do app, e também pra onde o `index.html` manda quem abre o site.

O que tem na tela.

* Marca FinSight com a tagline Wealth Intelligence.
* Campos de email e senha, com botão pra mostrar ou esconder a senha.
* Caixa de Lembrar de mim e link de Esqueci minha senha.
* Botão Entrar.
* Botões de login social com Google e GitHub.
* Link pra criar conta.

Como funciona. Ao enviar, o formulário chama `dbLogin(email, senha)`. Dando certo, guarda o usuário e a sessão no `localStorage` e manda pra o dashboard. Erros aparecem numa mensagem acima do botão. Se já existe uma sessão ativa, a tela vai direto pra o dashboard.

## Criar conta (pages/register.html)

Cadastro de novo usuário.

O que tem na tela.

* Campos de nome completo, email, senha e confirmação de senha.
* Medidor de força da senha, com três barras e um rótulo de Fraca, Média ou Forte, que reage conforme você digita.
* Caixa de aceite dos Termos de Uso e da Política de Privacidade.
* Botão Criar conta.
* Botões de login social com Google e Apple.

Como funciona. Valida se os campos foram preenchidos, se as senhas batem e se os termos foram aceitos. Depois chama `dbRegister(nome, email, senha)`. Se o email já existir, avisa que ele já está cadastrado. Dando certo, manda pra o login.

## Dashboard (pages/dashboard.html)

É a tela principal depois do login. Concentra a visão geral do mês.

O que tem na tela.

* Banner de alerta, que aparece quando as despesas do mês passam das receitas.
* Navegação por mês, com setas pra voltar e avançar.
* Saldo total em destaque. No mês atual mostra o saldo real das carteiras. Em meses passados mostra o resultado do mês, que é receita menos despesa.
* Cards de receitas e despesas do mês.
* Gráfico de pizza em formato de rosca, desenhado em SVG, com os gastos da semana por categoria e uma legenda.
* Gráfico de barras com o balanço dos últimos seis meses, comparando receita e despesa, com uma dica ao passar o mouse.
* Metas em miniatura e a lista das transações recentes.
* Botão flutuante que abre o seletor de tipo de lançamento.
* Barra de navegação embaixo e ícone de notificações no topo.

Como funciona. Os dados vêm do banco através de `dbGetContas`, `dbGetTodasTransacoes`, `dbGetMetas` e `dbGetCategorias`. O botão flutuante abre o seletor com quatro tipos de lançamento, que são Despesa, Receita, Transferência e Despesa de Cartão. Despesa e Receita salvam com `dbAddTransacao` e, quando marcadas como efetivadas, atualizam o saldo da conta com `dbUpdateSaldo`. A Transferência move o valor entre duas carteiras, registrando as duas pernas e ajustando os dois saldos. O ícone de notificações abre uma lista gerada na hora, com avisos de despesas acima das receitas, transações pendentes, metas perto do prazo e carteiras zeradas. Um ponto vermelho aparece quando existe algum alerta.

## Atividade (pages/transactions.html)

É a lista completa das transações, com busca e filtros.

O que tem na tela.

* Navegação por mês e cards com o total de receitas e despesas do mês.
* Campo de busca por título ou descrição.
* Abas de filtro de Todas, Receitas, Despesas, Transferências e Pendentes.
* Lista de transações agrupada por data, com rótulos de Hoje e Ontem.
* Em cada linha aparece o ícone e a cor da categoria, o título, a conta, o valor e selos de Pendente ou de parcelas, como 3x.
* Modais de detalhe da transação, confirmação de exclusão, nova despesa, nova receita, transferência e despesa de cartão.
* Botão flutuante e navegação embaixo.

Como funciona. Clicar numa transação abre o modal de detalhe, com data, categoria, conta e status, e dali dá pra editar ou excluir. O botão de marcação em cada linha alterna entre efetivada e pendente, usando `dbUpdateTransacao`. Nova Despesa aceita descrição opcional e parcelamento de 2x até 12x. Nova Receita é parecida, sem parcelas. As duas salvam com `dbAddTransacao` e ajustam o saldo com `dbUpdateSaldo`. Editar reabre o lançamento preenchido e salva as mudanças, corrigindo o saldo pela diferença. Excluir reverte o saldo e remove o registro com `dbDeleteTransacao`. A Transferência registra o movimento entre duas carteiras e atualiza os dois saldos.

## Carteiras (pages/wallets.html)

É onde o usuário gerencia as contas, ou carteiras.

O que tem na tela.

* Saldo total somando todas as carteiras e a contagem de carteiras.
* Botão de Nova carteira.
* Lista de cards de carteira, cada um com ícone, nome, saldo, link de Ver transações e botão de Excluir.
* Modais de nova carteira e de confirmação de exclusão.

Como funciona. A lista vem de `dbGetContas`, e o saldo de cada carteira é lido do seu tipo padrão. Nova carteira pede nome e saldo inicial e cria com `dbAddContaComSaldo`. Excluir abre uma confirmação e remove a carteira com `dbDeleteConta`.

## Metas (pages/goals.html)

São as metas de poupança, com acompanhamento de progresso.

O que tem na tela.

* Abas de filtro de Todas, Em andamento e Concluídas.
* Lista de cards de meta, com nome, ícone, status ou prazo, valor atual sobre valor alvo, percentual e barra de progresso.
* Botão de Aporte nas metas em andamento e botão de excluir.
* Modais de nova meta e de confirmação de exclusão.
* Botão de adicionar no topo pra criar meta.

Como funciona. As metas vêm de `dbGetMetas`. Uma meta é considerada concluída quando o percentual chega a 100%. Nova meta pede nome, valor alvo, valor inicial opcional, prazo opcional, conta vinculada e um ícone, escolhido numa grade de doze opções. Ela salva com `dbAddMeta`. O nome e o ícone são guardados juntos como JSON. O Aporte soma um valor ao valor atual da meta com `dbAporteMeta`, sem passar do alvo. Excluir remove a meta com `dbDeleteMeta`.

## Categorias (pages/categories.html)

Cadastro e listagem de categorias.

O que tem na tela.

* Abas de filtro de Todas, Receita e Despesa.
* Card de Insight Semanal, com um resumo de gasto da semana.
* Grade de categorias, cada uma com ícone, cor, selo de Receita ou Despesa, nome e botão de excluir.
* Modal de nova categoria, com seletor de tipo, grade de ícones, grade de cores e uma prévia ao vivo do card.

Como funciona. As categorias vêm de `dbGetCategorias`. Cada categoria é guardada como JSON no campo `nome_categoria`, com nome, ícone, cor e tipo. No modal, escolher tipo, ícone, cor ou digitar o nome atualiza a prévia na hora. O botão Criar monta o JSON e salva com `dbAddCategoria`. Excluir remove a categoria com `dbDeleteCategoria`.

## Relatórios (pages/reports.html)

Análise das transações por período.

O que tem na tela.

* Abas de período de Mês, Trimestre, Ano e Personalizado.
* Cards de receitas e despesas e um destaque de saldo do período, com um selo indicando se as receitas passaram das despesas ou o contrário.
* Gráfico de barras, que muda conforme o período, mostrando os últimos sete dias, os últimos três meses, os meses do ano ou faixas do intervalo personalizado.
* Quebra por categoria, separando Entradas e Saídas com o total de cada uma.
* Botão de exportar relatório em CSV.
* Modal de filtros, com período personalizado de e até, tipo, categoria e conta.

Como funciona. Os dados vêm de `dbGetTodasTransacoes`, `dbGetCategorias` e `dbGetContas`. Trocar de aba recalcula tudo, ou seja, os totais, o gráfico e a quebra por categoria. O modal de filtros refina por intervalo de datas, tipo, categoria e conta. Escolher datas joga o período pra o modo Personalizado. A exportação gera um arquivo CSV com data, título, categoria, conta, tipo e valor de cada transação do período.

## Perfil (pages/profile.html)

Configurações da conta e segurança.

O que tem na tela.

* Avatar com as iniciais e nome do usuário.
* Informações pessoais, onde dá pra editar o nome completo.
* Preferências, com a moeda em BRL e o formato de data em DD/MM/AAAA.
* Segurança protegida por PIN. Enquanto está travada, mostra o botão pra inserir ou criar o PIN. Destravada, libera trocar email, trocar senha e gerenciar o PIN.
* Sobre, com a versão do app, os Termos de Uso e a Política de Privacidade.
* Botões de Sair da conta e de Excluir minha conta.

Como funciona. Salvar nome, trocar email e trocar senha atualizam direto a tabela `cliente` no Supabase, usando o cliente `_db`, e também atualizam o `localStorage`. As confirmações aparecem num toast. O PIN tem quatro dígitos, com teclado na tela e suporte ao teclado físico. Ele aceita criar, verificar, confirmar e trocar, e fica guardado no `localStorage`. PIN errado faz os campos tremerem. Sair da conta limpa o `localStorage` e volta pra o login.

## Tema claro e escuro

Não é uma tela, mas vale registrar. O app inteiro tem troca de tema claro e escuro. O padrão é o escuro, a preferência fica salva no `localStorage` e um botão de tema fica disponível no topo das páginas internas.
