# Sobre o projeto

## O que é o FinSight

O FinSight é uma aplicação web de finanças pessoais. A ideia é dar ao usuário um lugar único para registrar e enxergar a própria vida financeira: quanto entra, quanto sai, em que categorias o dinheiro está sendo gasto e o quanto falta para bater as metas de poupança.

Na prática, o app gira em torno de quatro coisas:

- **Carteiras** — as contas onde o dinheiro fica (Nubank, poupança, dinheiro físico etc.).
- **Transações** — cada receita ou despesa lançada, com data, valor, categoria e conta.
- **Categorias** — a forma de classificar os gastos e ganhos (alimentação, transporte, salário…).
- **Metas** — objetivos de poupança com valor alvo, valor atual e prazo.

A partir desses dados, o FinSight monta um dashboard com saldo, gráficos e alertas, além de uma tela de relatórios com filtros e exportação para CSV.

## O problema que ele resolve

A motivação do projeto é o consumo impulsivo e a dificuldade de controle financeiro na era digital. Com pagamentos por aproximação, compras em um clique e crédito fácil, fica simples gastar sem perceber o quanto já foi.

O FinSight ataca isso de forma direta:

- Centraliza os lançamentos para que o usuário **veja** os gastos em vez de só senti-los no fim do mês.
- Mostra um **alerta no dashboard quando as despesas do mês superam as receitas**, sinalizando o problema na hora certa.
- Destaca **transações pendentes** (lançamentos ainda não efetivados), ajudando a não perder contas de vista.
- Permite definir **metas de poupança** para dar um objetivo concreto ao dinheiro guardado.

## Contexto acadêmico

O FinSight foi desenvolvido na disciplina de **Projeto Integrador** do curso de **Análise e Desenvolvimento de Sistemas (ADS)** do **SENAI**, e submetido à **SAGA SENAI de Inovação**.

## Objetivos do sistema

Olhando para o que o código realmente entrega, os objetivos do FinSight são:

1. Permitir cadastro e login de usuários.
2. Gerenciar carteiras (contas) com saldo.
3. Registrar receitas e despesas, com suporte a parcelamento e ao status de efetivada/pendente.
4. Classificar transações por categorias personalizáveis (com ícone, cor e tipo).
5. Criar e acompanhar metas de poupança com progresso e prazo.
6. Apresentar um dashboard com saldo total, receitas/despesas do mês, gráfico de gastos por categoria e balanço dos últimos 6 meses.
7. Gerar relatórios por período (mês, trimestre, ano ou personalizado) com filtros e exportação em CSV.
8. Oferecer ajustes de perfil e uma área de segurança protegida por PIN.
