# Documentação do Arquivo `Comment.java`

## Descrição Geral

A classe `Comment` é uma representação de um comentário no sistema. Ela encapsula os dados de um comentário, como ID, nome de usuário, corpo do comentário e a data de criação. Além disso, fornece métodos para criar, buscar, deletar e persistir comentários em um banco de dados PostgreSQL.

---

## Estrutura de Dados

### Atributos da Classe `Comment`

| Atributo       | Tipo         | Descrição                                                                 |
|----------------|--------------|---------------------------------------------------------------------------|
| `id`           | `String`     | Identificador único do comentário (UUID).                                |
| `username`     | `String`     | Nome de usuário associado ao comentário.                                 |
| `body`         | `String`     | Conteúdo do comentário.                                                  |
| `created_on`   | `Timestamp`  | Data e hora em que o comentário foi criado.                              |

---

## Métodos

### Construtor

```java
public Comment(String id, String username, String body, Timestamp created_on)
```

- **Descrição**: Inicializa uma instância da classe `Comment` com os valores fornecidos.
- **Parâmetros**:
  - `id`: Identificador único do comentário.
  - `username`: Nome de usuário associado ao comentário.
  - `body`: Conteúdo do comentário.
  - `created_on`: Data e hora de criação do comentário.

---

### `create`

```java
public static Comment create(String username, String body)
```

- **Descrição**: Cria um novo comentário e o persiste no banco de dados.
- **Parâmetros**:
  - `username`: Nome de usuário associado ao comentário.
  - `body`: Conteúdo do comentário.
- **Retorno**: Um objeto `Comment` criado.
- **Exceções**:
  - Lança `BadRequest` se o comentário não puder ser salvo.
  - Lança `ServerError` em caso de erro inesperado.

---

### `fetch_all`

```java
public static List<Comment> fetch_all()
```

- **Descrição**: Recupera todos os comentários armazenados no banco de dados.
- **Retorno**: Uma lista de objetos `Comment`.

---

### `delete`

```java
public static Boolean delete(String id)
```

- **Descrição**: Deleta um comentário do banco de dados com base no ID fornecido.
- **Parâmetros**:
  - `id`: Identificador único do comentário a ser deletado.
- **Retorno**: `true` se o comentário foi deletado com sucesso, caso contrário, `false`.

---

### `commit`

```java
private Boolean commit() throws SQLException
```

- **Descrição**: Persiste o comentário atual no banco de dados.
- **Retorno**: `true` se o comentário foi salvo com sucesso, caso contrário, `false`.
- **Exceções**:
  - Lança `SQLException` em caso de falha na execução da query.

---

## Insights

1. **Conexão com o Banco de Dados**:
   - A classe utiliza uma conexão com o banco de dados PostgreSQL, fornecida pela classe `Postgres`. Certifique-se de que a classe `Postgres` está corretamente implementada e configurada.

2. **Tratamento de Exceções**:
   - O método `create` utiliza exceções personalizadas (`BadRequest` e `ServerError`) para lidar com erros. Certifique-se de que essas classes estão implementadas no projeto.
   - O método `delete` retorna `false` em caso de falha, mas não propaga a exceção, o que pode dificultar o rastreamento de erros.

3. **Segurança**:
   - O uso de `PreparedStatement` para as queries SQL ajuda a prevenir ataques de injeção de SQL.
   - No entanto, o método `fetch_all` utiliza uma query SQL direta (`Statement`), o que pode ser vulnerável a injeções de SQL se parâmetros forem adicionados no futuro.

4. **Gerenciamento de Recursos**:
   - A conexão com o banco de dados é fechada no método `fetch_all`, mas não há garantia de fechamento em outros métodos. Considere o uso de `try-with-resources` para garantir o fechamento automático.

5. **UUID para Identificação**:
   - O uso de `UUID` para o atributo `id` garante unicidade global para os comentários.

6. **Timestamp**:
   - A classe utiliza `Timestamp` para registrar a data e hora de criação do comentário, o que é útil para ordenação e auditoria.

---

## Dependências Externas

- **Bibliotecas Importadas**:
  - `java.sql.*`: Para manipulação de conexões, queries e resultados do banco de dados.
  - `java.util.*`: Para manipulação de listas, datas e UUIDs.
  - `org.apache.catalina.Server`: Importado, mas não utilizado no código fornecido.

- **Classes Externas**:
  - `Postgres`: Fornece a conexão com o banco de dados PostgreSQL.
  - `BadRequest` e `ServerError`: Exceções personalizadas utilizadas para tratamento de erros.

---

## Possíveis Melhorias

1. **Refatoração do Método `fetch_all`**:
   - Substituir o uso de `Statement` por `PreparedStatement` para maior segurança.

2. **Gerenciamento de Conexões**:
   - Implementar `try-with-resources` para garantir o fechamento de conexões e evitar vazamentos de recursos.

3. **Documentação de Exceções**:
   - Documentar todas as exceções que podem ser lançadas pelos métodos.

4. **Validação de Dados**:
   - Adicionar validações para os parâmetros de entrada nos métodos `create` e `delete`.

5. **Remoção de Importações Não Utilizadas**:
   - A importação de `org.apache.catalina.Server` não é utilizada e pode ser removida.
