# Levantamento de Requisitos — FinSight

Documento de levantamento de requisitos do FinSight, uma aplicação web de finanças pessoais. Os requisitos abaixo refletem o que o sistema entrega hoje, com base na análise do código (telas, scripts e banco de dados).

## 1. Objetivo do sistema

Ajudar o usuário a registrar e acompanhar a própria vida financeira em um só lugar, controlando carteiras, receitas, despesas, transferências, categorias e metas de poupança, com painel de resumo, relatórios e alertas que ajudam a evitar o gasto sem controle.

## 2. Escopo

O FinSight cobre o ciclo básico de finanças pessoais de um usuário individual.

* Cadastro e acesso do usuário.
* Gestão de carteiras e saldos.
* Lançamento de receitas, despesas e transferências.
* Organização por categorias.
* Metas de poupança com acompanhamento.
* Painel com resumo do mês, gráficos e notificações.
* Relatórios por período com exportação.
* Perfil e segurança da conta.

## 3. Atores

| Ator | Descrição |
|------|-----------|
| Usuário | Pessoa que usa o FinSight para controlar as próprias finanças. É o único perfil do sistema e tem acesso a todas as funções após o login. |

## 4. Requisitos Funcionais

Prioridade: **Essencial** é o que o sistema precisa para funcionar, **Importante** agrega valor central, **Desejável** é complemento.

### 4.1 Acesso e conta

| ID | Requisito | Prioridade |
|----|-----------|------------|
| RF01 | Permitir o cadastro de usuário com nome, email e senha, com confirmação de senha, indicador de força da senha e aceite dos termos. | Essencial |
| RF02 | Permitir login com email e senha. | Essencial |
| RF03 | Manter a sessão do usuário e proteger as páginas internas, redirecionando para o login quando não há sessão ativa. | Essencial |
| RF04 | Permitir que o usuário saia da conta (logout). | Essencial |

### 4.2 Carteiras

| ID | Requisito | Prioridade |
|----|-----------|------------|
| RF05 | Permitir criar uma carteira com nome e saldo inicial. | Essencial |
| RF06 | Listar as carteiras com o saldo de cada uma e o saldo total somado. | Essencial |
| RF07 | Permitir editar o nome de uma carteira. | Importante |
| RF08 | Permitir excluir uma carteira junto com as transações ligadas a ela. | Importante |

### 4.3 Categorias

| ID | Requisito | Prioridade |
|----|-----------|------------|
| RF09 | Permitir criar uma categoria com nome, ícone, cor e tipo (receita ou despesa), com pré-visualização. | Essencial |
| RF10 | Listar as categorias com filtro por tipo (todas, receita ou despesa). | Importante |
| RF11 | Permitir excluir uma categoria. | Importante |

### 4.4 Transações

| ID | Requisito | Prioridade |
|----|-----------|------------|
| RF12 | Permitir registrar uma despesa com valor, título, descrição opcional, categoria, conta, data, parcelamento opcional e status de efetivada ou pendente. | Essencial |
| RF13 | Permitir registrar uma receita com valor, título, descrição opcional, categoria, conta, data e status de efetivada ou pendente. | Essencial |
| RF14 | Permitir editar uma transação, ajustando o saldo da conta conforme a mudança. | Importante |
| RF15 | Permitir excluir uma transação, revertendo o efeito dela no saldo. | Importante |
| RF16 | Permitir alternar uma transação entre efetivada e pendente direto na lista. | Importante |
| RF17 | Listar as transações agrupadas por data, com busca por título ou descrição e filtros por tipo (todas, receitas, despesas, transferências e pendentes). | Essencial |
| RF18 | Permitir navegar entre os meses na lista de transações. | Importante |
| RF19 | Ao lançar, mostrar apenas as categorias do tipo correspondente (despesa mostra categorias de despesa, receita mostra de receita). | Importante |

### 4.5 Transferências

| ID | Requisito | Prioridade |
|----|-----------|------------|
| RF20 | Permitir transferir um valor entre duas carteiras, registrando a saída na origem e a entrada no destino e atualizando os dois saldos. | Importante |

### 4.6 Metas

| ID | Requisito | Prioridade |
|----|-----------|------------|
| RF21 | Permitir criar uma meta com nome, valor alvo, valor inicial opcional, prazo opcional, conta vinculada e ícone. | Essencial |
| RF22 | Listar as metas com progresso em percentual e barra, e filtro por situação (todas, em andamento, concluídas). | Essencial |
| RF23 | Permitir editar uma meta. | Importante |
| RF24 | Permitir registrar um aporte em uma meta, somando ao valor atual sem passar do valor alvo. | Importante |
| RF25 | Permitir excluir uma meta. | Importante |

### 4.7 Painel (Dashboard)

| ID | Requisito | Prioridade |
|----|-----------|------------|
| RF26 | Exibir o saldo total das carteiras. | Essencial |
| RF27 | Exibir o total de receitas e de despesas do mês. | Essencial |
| RF28 | Alertar quando as despesas do mês superam as receitas. | Importante |
| RF29 | Exibir um gráfico dos gastos da semana por categoria. | Importante |
| RF30 | Exibir um gráfico do balanço dos últimos seis meses, comparando receita e despesa. | Importante |
| RF31 | Exibir as metas em destaque e as transações mais recentes. | Importante |
| RF32 | Exibir uma central de notificações com alertas de despesas acima das receitas, transações pendentes, metas perto do prazo e carteiras zeradas. | Desejável |
| RF33 | Permitir navegar entre os meses no painel. | Importante |

### 4.8 Relatórios

| ID | Requisito | Prioridade |
|----|-----------|------------|
| RF34 | Permitir gerar relatório por período (mês, trimestre, ano ou intervalo personalizado). | Importante |
| RF35 | Exibir os totais de receitas, despesas e saldo do período. | Importante |
| RF36 | Exibir um gráfico de evolução conforme o período escolhido. | Importante |
| RF37 | Exibir a quebra por categoria, separando entradas e saídas. | Importante |
| RF38 | Permitir filtrar o relatório por tipo, categoria, conta e intervalo de datas. | Desejável |
| RF39 | Permitir exportar o relatório em CSV. | Desejável |

### 4.9 Perfil e segurança

| ID | Requisito | Prioridade |
|----|-----------|------------|
| RF40 | Permitir editar o nome do usuário. | Importante |
| RF41 | Permitir trocar o email da conta. | Importante |
| RF42 | Permitir trocar a senha da conta. | Importante |
| RF43 | Permitir alterar a foto de perfil. | Desejável |
| RF44 | Proteger a área de segurança com um PIN, permitindo criar, verificar e trocar o PIN. | Desejável |
| RF45 | Permitir excluir a conta, removendo os dados do usuário. | Importante |

### 4.10 Interface e preferências

| ID | Requisito | Prioridade |
|----|-----------|------------|
| RF46 | Permitir alternar entre tema claro e escuro, guardando a preferência. | Desejável |
| RF47 | Adaptar o layout ao tamanho da tela, com barra inferior no celular e barra lateral no desktop. | Importante |

## 5. Requisitos Não Funcionais

| ID | Requisito |
|----|-----------|
| RNF01 | O sistema deve ser uma aplicação web acessível por navegador. |
| RNF02 | O front deve ser feito em HTML, CSS e JavaScript, sem framework. |
| RNF03 | Os dados devem ser persistidos em um banco PostgreSQL gerenciado pelo Supabase. |
| RNF04 | A interface deve ser responsiva, funcionando em celular e em desktop. |
| RNF05 | A interface deve oferecer tema claro e escuro. |
| RNF06 | Os valores monetários devem ser exibidos em real, no formato pt-BR. |
| RNF07 | As datas devem ser exibidas no formato DD/MM/AAAA. |
| RNF08 | A iconografia deve usar Material Symbols e as fontes devem vir do Google Fonts. |
| RNF09 | A sessão e as preferências do usuário devem ser guardadas no navegador. |
| RNF10 | O sistema deve dar retorno ao usuário em cada ação, com mensagens de erro, confirmações de exclusão e avisos de sucesso. |

## 6. Regras de Negócio

| ID | Regra |
|----|-------|
| RN01 | O email do usuário deve ser único no sistema. |
| RN02 | Para registrar uma transação, o usuário precisa ter pelo menos uma carteira e uma categoria. |
| RN03 | Uma despesa diminui o saldo da carteira e uma receita aumenta. |
| RN04 | Ao editar uma transação, o saldo da carteira é recalculado conforme a diferença entre o valor antigo e o novo. |
| RN05 | Ao excluir uma transação, o efeito dela no saldo é revertido. |
| RN06 | Uma transferência gera duas transações, uma de saída na carteira de origem e uma de entrada na de destino, e não é contada como receita nem despesa nos totais. |
| RN07 | Em uma transferência, a carteira de origem e a de destino devem ser diferentes. |
| RN08 | Uma meta é considerada concluída quando o valor atual alcança o valor alvo. |
| RN09 | Um aporte em meta não pode ultrapassar o valor alvo. |
| RN10 | Cada categoria pertence a um tipo, receita ou despesa, e o lançamento só oferece categorias do tipo correspondente. |
| RN11 | Ao excluir uma carteira, as transações ligadas a ela são removidas. Ao excluir a conta, todos os dados do usuário são removidos. |

## 7. Visão geral das telas

| Tela | Função principal |
|------|------------------|
| Login e Cadastro | Acesso e criação de conta. |
| Dashboard | Resumo do mês, gráficos, metas em destaque, recentes e notificações. |
| Atividade | Lista de transações com busca, filtros e navegação por mês. |
| Carteiras | Gestão das contas e saldos. |
| Metas | Criação e acompanhamento das metas de poupança. |
| Categorias | Cadastro e organização das categorias. |
| Relatórios | Análise por período com exportação em CSV. |
| Perfil | Dados da conta, segurança por PIN e preferências. |
