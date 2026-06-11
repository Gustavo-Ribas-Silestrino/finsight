# Banco de dados

O banco é um Postgres rodando no Supabase. O schema está em `supabase_schema.sql` e tem cinco tabelas, que são `cliente`, `conta`, `tipo`, `categoria` e `transacao`.

## Tabelas

### cliente

São os usuários do sistema.

* **`id_cliente`.** `SERIAL`, chave primária.
* **`username`.** `VARCHAR(50)`, não nulo.
* **`email`.** `VARCHAR(100)`, não nulo e único.
* **`password`.** `VARCHAR(255)`, não nulo.

### conta

São as carteiras de cada usuário, como Nubank, poupança ou dinheiro físico. Na interface, isso aparece como Carteira.

* **`id_conta`.** `SERIAL`, chave primária.
* **`id_cliente`.** `INT`, não nulo, chave estrangeira pra `cliente(id_cliente)`.
* **`nome_banco`.** `VARCHAR(100)`, não nulo.

### tipo

Essa tabela tem um papel duplo. Ela guarda tanto o saldo de uma carteira quanto as metas de poupança, sempre ligada a uma `conta`.

* Quando `saldo_objetivo` está nulo, a linha representa o saldo padrão da carteira, e o `saldo_atual` é o dinheiro disponível.
* Quando `saldo_objetivo` está preenchido, a linha representa uma meta, com valor alvo, valor atual e prazo. Nesse caso, o campo `nome` guarda um JSON com nome e ícone.

As colunas.

* **`id_tipo`.** `SERIAL`, chave primária.
* **`nome`.** `VARCHAR(50)`, não nulo.
* **`saldo_objetivo`.** `DECIMAL(10,2)`.
* **`saldo_atual`.** `DECIMAL(10,2)`.
* **`data_limite`.** `DATE`.
* **`id_conta`.** `INT`, não nulo, chave estrangeira pra `conta(id_conta)`.

### categoria

São as categorias de receita e despesa. O nome guarda um JSON com nome, ícone, cor e tipo. Por isso o campo comporta texto estruturado.

* **`id_categoria`.** `SERIAL`, chave primária.
* **`nome_categoria`.** `VARCHAR(50)`, não nulo.

### transacao

É cada lançamento de receita ou despesa.

* **`id_transacao`.** `SERIAL`, chave primária.
* **`valor`.** `DECIMAL(10,2)`, não nulo.
* **`data_transacao`.** `DATE`, não nulo.
* **`descricao`.** `VARCHAR(255)`.
* **`titulo`.** `VARCHAR(100)`, não nulo.
* **`id_categoria`.** `INT`, não nulo, chave estrangeira pra `categoria(id_categoria)`.
* **`quantidade_parcelas`.** `INT`.
* **`id_tipo`.** `INT`, não nulo, chave estrangeira pra `tipo(id_tipo)`.
* **`debitocredito`.** `BOOLEAN`, não nulo.
* **`efetividade`.** `BOOLEAN`, não nulo.
* **`data_efetividade`.** `DATE`.

Dois campos booleanos definem a natureza da transação.

* **`debitocredito`.** Vale `true` pra receita, que é crédito, e `false` pra despesa, que é débito. É o que o código usa pra separar entrada de saída.
* **`efetividade`.** Vale `true` se a transação já foi efetivada, e `false` se ainda está pendente. O campo `data_efetividade` registra quando ela foi efetivada.

## Relacionamentos

A relação entre as tabelas segue uma cadeia simples.

* Um `cliente` tem várias `conta`, pela coluna `conta.id_cliente`.
* Uma `conta` tem vários `tipo`, pela coluna `tipo.id_conta`. Isso vale tanto pro saldo da carteira quanto pras metas.
* Um `tipo` tem várias `transacao`, pela coluna `transacao.id_tipo`.
* Uma `categoria` classifica várias `transacao`, pela coluna `transacao.id_categoria`.

Todas essas relações são garantidas por chaves estrangeiras, com as restrições nomeadas como `fk_conta_cliente`, `fk_tipo_conta`, `fk_transacao_categoria` e `fk_transacao_tipo`.

## Sobre a normalização

O modelo está normalizado de forma consistente.

* **Sem repetição de dados.** Usuário, conta, tipo, categoria e transação ficam cada um na sua tabela. As transações só apontam pra os outros registros por id, em vez de repetir nome de conta ou de categoria.
* **Chaves primárias artificiais.** Todas as tabelas usam `SERIAL`, deixando as relações sempre por id.
* **Integridade referencial.** As chaves estrangeiras cobrem todos os vínculos, o que evita transação órfã, sem conta ou sem categoria.
* **Formas normais.** O modelo atende às formas normais usuais. Cada coluna guarda um valor único e os atributos dependem da chave da própria tabela.

Um ponto de design que vale registrar. As categorias e o nome das metas guardam um JSON dentro de um campo de texto, com informações de apresentação como ícone, cor e tipo. É uma escolha proposital que deixa o schema enxuto e flexível, sem precisar de colunas extras pra cada detalhe visual. Quem monta e lê esse JSON é o JavaScript do front.
