# Funcionalidades

Cada arquivo dentro de `pages/` é uma tela do app. Abaixo, o que cada uma faz, seus principais elementos e como o usuário interage com ela.

> Observação honesta: algumas telas têm modais que ainda são **visuais** (formulário montado, mas sem lógica de salvar conectada ao banco). Esses casos estão marcados como "estático" para não passar a ideia errada do que está pronto.

---

## Login — `pages/login.html`

Tela de entrada do app. É também para onde o `index.html` redireciona ao abrir.

**Elementos principais:**
- Marca FinSight com a tagline "Wealth Intelligence".
- Campos de e-mail e senha, com botão de mostrar/ocultar senha.
- Checkbox "Lembrar de mim" e link "Esqueci minha senha" (o link aponta para `#`).
- Botão "Entrar".
- Botões sociais "Google" e "GitHub" (apenas visuais, sem handler).
- Link para a tela de criar conta.

**Interação:**
- Ao enviar, o formulário chama `dbLogin(email, senha)`. Se der certo, guarda o usuário e a sessão no `localStorage` (`finsight-user` e `finsight-session`) e redireciona para o dashboard.
- Erros (campos vazios ou falha de login) aparecem como uma mensagem em vermelho acima do botão.
- Se já houver sessão ativa, a tela pula direto para o dashboard.

---

## Criar conta — `pages/register.html`

Cadastro de novo usuário.

**Elementos principais:**
- Campos de nome completo, e-mail, senha e confirmação de senha.
- **Medidor de força da senha** com três barras e um rótulo (Fraca / Média / Forte), que reage conforme você digita.
- Checkbox de aceite dos Termos de Uso e Política de Privacidade.
- Botão "Criar conta".
- Botões sociais "Google" e "Apple" (apenas visuais).

**Interação:**
- Valida campos preenchidos, se as senhas coincidem e se os termos foram aceitos.
- Chama `dbRegister(nome, email, senha)`. Se o e-mail já existir (erro `23505`), mostra "Este e-mail já está cadastrado.".
- Em caso de sucesso, redireciona para o login.

---

## Dashboard — `pages/dashboard.html`

Tela principal depois do login. Concentra a visão geral do mês.

**Elementos principais:**
- **Banner de alerta**: aparece quando as despesas do mês superam as receitas.
- **Banner de lembrete** de conta fixa (fica oculto por padrão até haver dados).
- **Navegação por mês** (setas para voltar/avançar; não passa do mês atual).
- **Saldo total** em destaque. No mês atual mostra o saldo real das carteiras; em meses passados mostra o resultado do mês (receitas − despesas).
- **Cards de receitas e despesas** do mês.
- **Gráfico de pizza** (donut em SVG) com os gastos da semana por categoria, com legenda.
- **Gráfico de barras** com o balanço dos últimos 6 meses (receita vs. despesa por mês, com tooltip ao passar o mouse).
- **Metas** em miniatura (até 4) e lista de **transações recentes** (últimas 5).
- **Botão flutuante (FAB)** que abre um seletor de tipo de lançamento.
- Barra de navegação inferior e ícone de notificações na appbar.

**Interação:**
- Os dados vêm do banco via `dbGetContas`, `dbGetTodasTransacoes`, `dbGetMetas` e `dbGetCategorias`.
- O FAB abre o modal "O que deseja registrar?" com quatro opções: Despesa, Receita, Transferência e Despesa de Cartão.
- **Nova Despesa** e **Nova Receita** salvam de verdade: gravam a transação (`dbAddTransacao`) e, se marcadas como efetivadas, atualizam o saldo da conta (`dbUpdateSaldo`).
- Os modais de **Transferência** e **Despesa de Cartão** são estáticos (opções fixas, sem salvar).
- O ícone de notificações abre uma lista gerada na hora: despesas acima das receitas, transações pendentes, metas perto do prazo e carteiras zeradas. Um badge vermelho aparece quando há algum alerta.

---

## Atividade (transações) — `pages/transactions.html`

Lista completa das transações, com busca e filtros.

**Elementos principais:**
- Navegação por mês e cards de total de receitas/despesas do mês.
- Campo de **busca** por título ou descrição.
- **Abas de filtro**: Todas, Receitas, Despesas, Transferências e Pendentes.
- Lista de transações **agrupada por data** (com rótulos "Hoje" e "Ontem").
- Cada linha mostra ícone/cor da categoria, título, conta, valor e selos de "Pendente" ou de parcelas (ex.: "3x").
- Modais: detalhe da transação, confirmação de exclusão, nova despesa, nova receita, transferência e despesa de cartão.
- FAB e navegação inferior.

**Interação:**
- Clicar numa transação abre o modal de **detalhe** (data, categoria, conta, status).
- O botão de check em cada linha alterna entre efetivada/pendente (`dbUpdateTransacao`).
- **Nova Despesa** suporta descrição opcional e parcelamento (2x a 12x); **Nova Receita** é semelhante, sem parcelas. Ambas salvam via `dbAddTransacao` e ajustam o saldo (`dbUpdateSaldo`).
- Excluir uma transação reverte o saldo e remove o registro (`dbDeleteTransacao`).
- Os modais de transferência e cartão são estáticos.

---

## Carteiras — `pages/wallets.html`

Gerenciamento das contas/carteiras do usuário.

**Elementos principais:**
- **Saldo total** somando todas as carteiras e a contagem de carteiras.
- Botão "Nova carteira".
- Lista de cards de carteira, cada um com ícone, nome, saldo, link "Ver transações" e botão "Excluir".
- Modais: nova carteira, editar carteira (estático) e confirmação de exclusão.

**Interação:**
- A lista vem de `dbGetContas`; o saldo de cada carteira é lido do seu tipo padrão.
- **Nova carteira** pede nome e saldo inicial e cria via `dbAddContaComSaldo`.
- **Excluir** abre uma confirmação e remove a carteira (`dbDeleteConta`).
- O modal de **editar carteira** (nome, tipo, cor, "carteira principal") é estático.

---

## Metas — `pages/goals.html`

Metas de poupança com acompanhamento de progresso.

**Elementos principais:**
- Abas de filtro: Todas, Em andamento, Concluídas.
- Lista de cards de meta com nome, ícone, status (ou prazo), valor atual / valor alvo, percentual e barra de progresso.
- Botão de **Aporte** nas metas em andamento e botão de excluir.
- Modais: nova meta, editar meta (estático) e confirmação de exclusão.
- Botão "+" na appbar para criar meta.

**Interação:**
- As metas vêm de `dbGetMetas`. Uma meta é considerada concluída quando o percentual chega a 100%.
- **Nova meta** pede nome, valor alvo, valor inicial (opcional), prazo (opcional), conta vinculada e um ícone (grade de 12 opções). Salva via `dbAddMeta`. O nome e o ícone são guardados juntos como JSON.
- **Aporte** pede o valor por um `prompt()` e soma ao valor atual da meta (`dbAporteMeta`), sem ultrapassar o alvo.
- Excluir remove a meta (`dbDeleteMeta`).
- O modal de **editar meta** é estático.

---

## Categorias — `pages/categories.html`

Cadastro e listagem de categorias.

**Elementos principais:**
- Abas de filtro: Todas, Receita, Despesa.
- **Card de "Insight Semanal"** com texto e barra de percentual (valores fixos no HTML — estático).
- Grade de categorias, cada uma com ícone, cor, selo de Receita/Despesa, nome e botão de excluir.
- Modal de nova categoria com seletor de tipo (Despesa/Receita), grade de ícones, grade de cores e uma **pré-visualização ao vivo** do card.

**Interação:**
- As categorias vêm de `dbGetCategorias`. Cada categoria é guardada como **JSON** no campo `nome_categoria`, com nome, ícone, cor e tipo.
- No modal, escolher tipo, ícone, cor ou digitar o nome atualiza a pré-visualização em tempo real.
- "Criar" monta o JSON e salva via `dbAddCategoria`.
- Excluir pede confirmação (`confirm()`) e remove via `dbDeleteCategoria`.

---

## Relatórios — `pages/reports.html`

Análise das transações por período.

**Elementos principais:**
- Abas de período: Mês, Trimestre, Ano e Personalizado.
- Cards de receitas e despesas e um destaque de **saldo do período** com badge indicando se as receitas superaram as despesas (ou o contrário).
- **Gráfico de barras** que muda conforme o período (últimos 7 dias, últimos 3 meses, meses do ano ou faixas do intervalo personalizado).
- Quebra **por categoria**, separando Entradas e Saídas com os totais de cada uma.
- Botão "Exportar relatório (CSV)".
- Modal de filtros: período personalizado (de/até), tipo, categoria e conta.

**Interação:**
- Os dados vêm de `dbGetTodasTransacoes`, `dbGetCategorias` e `dbGetContas`.
- Trocar de aba recalcula tudo (totais, gráfico e quebra por categoria).
- O modal de filtros refina por intervalo de datas, tipo, categoria e conta; selecionar datas joga o período para "Personalizado".
- A exportação gera um arquivo CSV (`finsight_relatorio_AAAA-MM-DD.csv`) com data, título, categoria, conta, tipo e valor de cada transação do período.

---

## Perfil — `pages/profile.html`

Configurações da conta e segurança.

**Elementos principais:**
- Avatar com iniciais, nome do usuário e botão "Alterar foto".
- **Informações pessoais**: editar o nome completo.
- **Preferências**: moeda (BRL — R$) e formato de data (DD/MM/AAAA) — exibidos como informação fixa.
- **Segurança protegida por PIN**: enquanto bloqueada, mostra só o botão para inserir/criar PIN. Desbloqueada, libera alterar e-mail, alterar senha e gerenciar o PIN.
- **Sobre**: versão do app (2.0.0), Termos de Uso e Política de Privacidade.
- Botões "Sair da conta" e "Excluir minha conta".
- Modais: teclado de PIN e confirmação de exclusão de conta (estático).

**Interação:**
- Salvar nome, alterar e-mail e alterar senha atualizam direto a tabela `cliente` no Supabase (via o cliente `_db`), e atualizam o `localStorage`. Confirmações aparecem como um toast.
- O **PIN** tem 4 dígitos, com teclado na tela (e suporte ao teclado físico). Suporta criar, verificar, confirmar e alterar; fica guardado em `localStorage` (`finsight-pin`). PIN errado faz os campos "tremerem".
- "Sair da conta" limpa o `localStorage` e volta ao login.
- O botão "Excluir minha conta" abre um modal de confirmação estático.

---

## Tema claro/escuro

Não é uma tela, mas vale registrar: o app inteiro tem alternância de tema claro/escuro. O padrão é o escuro, a preferência fica salva em `localStorage` (`finsight-theme`) e um botão de tema é injetado na appbar das páginas internas.
