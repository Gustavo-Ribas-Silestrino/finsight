# Banco de dados

O banco é um Postgres rodando no Supabase. O schema está em `supabase_schema.sql` e tem cinco tabelas: `cliente`, `conta`, `tipo`, `categoria` e `transacao`.

## Tabelas

### `cliente`

Usuários do sistema.

| Coluna | Tipo | Restrições |
|--------|------|-----------|
| `id_cliente` | `SERIAL` | Chave primária |
| `username` | `VARCHAR(50)` | Não nulo |
| `email` | `VARCHAR(100)` | Não nulo, único |
| `password` | `VARCHAR(255)` | Não nulo |

### `conta`

As carteiras/contas de cada usuário (Nubank, poupança, dinheiro físico…). Na interface, isso aparece como "Carteira".

| Coluna | Tipo | Restrições |
|--------|------|-----------|
| `id_conta` | `SERIAL` | Chave primária |
| `id_cliente` | `INT` | Não nulo, FK → `cliente(id_cliente)` |
| `nome_banco` | `VARCHAR(100)` | Não nulo |

### `tipo`

Tabela com duplo papel. Ela guarda tanto o **saldo de uma carteira** quanto as **metas de poupança**, sempre vinculada a uma `conta`:

- Quando `saldo_objetivo` é nulo, a linha representa o **saldo padrão da carteira** (o `saldo_atual` é o dinheiro disponível).
- Quando `saldo_objetivo` está preenchido, a linha representa uma **meta** (com valor alvo, valor atual e prazo). Nesse caso, o campo `nome` guarda um JSON com `{ nome, icone }`.

| Coluna | Tipo | Restrições |
|--------|------|-----------|
| `id_tipo` | `SERIAL` | Chave primária |
| `nome` | `VARCHAR(50)` | Não nulo |
| `saldo_objetivo` | `DECIMAL(10,2)` | — |
| `saldo_atual` | `DECIMAL(10,2)` | — |
| `data_limite` | `DATE` | — |
| `id_conta` | `INT` | Não nulo, FK → `conta(id_conta)` |

### `categoria`

Categorias de receita/despesa. O nome guarda um JSON com `{ nome, icone, cor, tipo }` — por isso o campo comporta até 50 caracteres de texto estruturado.

| Coluna | Tipo | Restrições |
|--------|------|-----------|
| `id_categoria` | `SERIAL` | Chave primária |
| `nome_categoria` | `VARCHAR(50)` | Não nulo |

### `transacao`

Cada lançamento de receita ou despesa.

| Coluna | Tipo | Restrições |
|--------|------|-----------|
| `id_transacao` | `SERIAL` | Chave primária |
| `valor` | `DECIMAL(10,2)` | Não nulo |
| `data_transacao` | `DATE` | Não nulo |
| `descricao` | `VARCHAR(255)` | — |
| `titulo` | `VARCHAR(100)` | Não nulo |
| `id_categoria` | `INT` | Não nulo, FK → `categoria(id_categoria)` |
| `quantidade_parcelas` | `INT` | — |
| `id_tipo` | `INT` | Não nulo, FK → `tipo(id_tipo)` |
| `debitocredito` | `BOOLEAN` | Não nulo |
| `efetividade` | `BOOLEAN` | Não nulo |
| `data_efetividade` | `DATE` | — |

Dois campos booleanos definem a natureza da transação:

- **`debitocredito`** — `true` para receita (crédito), `false` para despesa (débito). É o que o código usa para separar entradas de saídas.
- **`efetividade`** — `true` se a transação já foi efetivada, `false` se ainda está pendente. `data_efetividade` registra quando foi efetivada.

## Relacionamentos

```
cliente (1) ───< (N) conta (1) ───< (N) tipo (1) ───< (N) transacao
                                                            >─── (1) categoria
```

- Um **cliente** tem várias **contas** (`conta.id_cliente`).
- Uma **conta** tem vários **tipos** (`tipo.id_conta`) — tanto o saldo da carteira quanto as metas.
- Um **tipo** tem várias **transações** (`transacao.id_tipo`).
- Uma **categoria** classifica várias **transações** (`transacao.id_categoria`).

Todas as relações são garantidas por chaves estrangeiras (`FOREIGN KEY`), com as constraints nomeadas: `fk_conta_cliente`, `fk_tipo_conta`, `fk_transacao_categoria` e `fk_transacao_tipo`.

## Sobre a normalização

O modelo está normalizado de forma consistente:

- **Sem repetição de dados**: usuário, conta, tipo, categoria e transação ficam cada um na sua tabela; as transações só referenciam os outros registros por id, em vez de repetir nome de conta ou de categoria.
- **Chaves primárias artificiais** (`SERIAL`) em todas as tabelas, deixando as relações sempre por id.
- **Integridade referencial** garantida por FKs em todos os vínculos, o que evita transações órfãs (sem conta ou sem categoria).
- Atende às formas normais usuais (1FN/2FN/3FN): cada coluna guarda um valor único e os atributos dependem da chave da própria tabela.

**Uma ressalva honesta:** as categorias e o nome das metas guardam um JSON dentro de um campo `VARCHAR`. Isso é um dado composto morando numa coluna de texto — na prática, uma quebra deliberada da 1FN para carregar ícone, cor e tipo sem precisar de colunas extras no schema. Funciona bem para o app, mas vale saber que essas informações não são consultáveis por SQL de forma direta; quem interpreta o JSON é o JavaScript.
