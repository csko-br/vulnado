# Documentação: `Comment.java`

## Descrição
A classe `Comment` é responsável por gerenciar comentários em um sistema. Ela fornece métodos para criar, buscar, deletar e salvar comentários em um banco de dados PostgreSQL. Cada comentário possui um identificador único (`id`), um nome de usuário (`username`), o corpo do comentário (`body`) e um timestamp (`created_on`) indicando quando foi criado.

---

## Estrutura de Dados

### Atributos
| Nome         | Tipo       | Descrição                                                                 |
|--------------|------------|---------------------------------------------------------------------------|
| `id`         | `String`   | Identificador único do comentário.                                       |
| `username`   | `String`   | Nome de usuário associado ao comentário.                                 |
| `body`       | `String`   | Conteúdo do comentário.                                                  |
| `created_on` | `Timestamp`| Data e hora em que o comentário foi criado.                              |

### Construtor
```java
public Comment(String id, String username, String body, Timestamp created_on)
```
Construtor que inicializa os atributos da classe com os valores fornecidos.

---

## Métodos

### `create(String username, String body)`
Cria um novo comentário e o salva no banco de dados.

#### Parâmetros
| Nome       | Tipo     | Descrição                          |
|------------|----------|------------------------------------|
| `username` | `String` | Nome de usuário do autor do comentário. |
| `body`     | `String` | Conteúdo do comentário.            |

#### Retorno
| Tipo       | Descrição                                      |
|------------|-----------------------------------------------|
| `Comment`  | Retorna o objeto `Comment` criado.            |

#### Exceções
- `BadRequest`: Lançada quando o comentário não pode ser salvo.
- `ServerError`: Lançada em caso de erro interno no servidor.

---

### `fetch_all()`
Busca todos os comentários armazenados no banco de dados.

#### Retorno
| Tipo            | Descrição                                      |
|-----------------|-----------------------------------------------|
| `List<Comment>` | Lista de objetos `Comment` recuperados.       |

#### Observações
- Utiliza uma consulta SQL para buscar todos os registros na tabela `comments`.
- Em caso de erro, o método imprime o stack trace e retorna uma lista vazia.

---

### `delete(String id)`
Deleta um comentário do banco de dados com base no seu identificador.

#### Parâmetros
| Nome | Tipo     | Descrição                          |
|------|----------|------------------------------------|
| `id` | `String` | Identificador único do comentário. |

#### Retorno
| Tipo      | Descrição                                      |
|-----------|-----------------------------------------------|
| `Boolean` | Retorna `true` se o comentário foi deletado com sucesso, caso contrário `false`. |

#### Observações
- Utiliza uma consulta SQL preparada para evitar injeção de SQL.
- Em caso de erro, o método imprime o stack trace e retorna `false`.

---

### `commit()`
Método privado que salva o comentário atual no banco de dados.

#### Retorno
| Tipo      | Descrição                                      |
|-----------|-----------------------------------------------|
| `Boolean` | Retorna `true` se o comentário foi salvo com sucesso, caso contrário `false`. |

#### Exceções
- `SQLException`: Lançada em caso de erro ao executar a consulta SQL.

---

## Insights

1. **Segurança**: 
   - O uso de `PreparedStatement` nos métodos `delete` e `commit` ajuda a prevenir ataques de injeção de SQL.
   - No entanto, o método `fetch_all` utiliza `Statement`, que é vulnerável a injeção de SQL. Recomenda-se substituir por `PreparedStatement`.

2. **Tratamento de Erros**:
   - O método `create` encapsula erros em exceções específicas (`BadRequest` e `ServerError`), o que melhora a legibilidade e o tratamento de erros.
   - Nos métodos `fetch_all` e `delete`, o tratamento de erros é limitado a imprimir o stack trace. Seria ideal lançar exceções ou retornar mensagens de erro mais detalhadas.

3. **Gerenciamento de Conexões**:
   - As conexões com o banco de dados são abertas em cada método, mas o fechamento nem sempre é garantido. Recomenda-se o uso de `try-with-resources` para garantir o fechamento automático das conexões.

4. **UUID para Identificação**:
   - O uso de `UUID` para gerar identificadores únicos garante que cada comentário tenha um ID exclusivo, reduzindo a chance de colisões.

5. **Timestamp**:
   - A classe utiliza `Timestamp` para registrar a data e hora de criação dos comentários, o que é útil para ordenação e auditoria.

6. **Dependências Externas**:
   - A classe depende de uma conexão com o banco de dados PostgreSQL, gerenciada pela classe `Postgres`. Certifique-se de que esta classe esteja corretamente implementada e configurada.

---

## Metadados
| Nome do Arquivo | Descrição                                   |
|------------------|-------------------------------------------|
| `Comment.java`   | Classe para gerenciar comentários em um sistema. |
