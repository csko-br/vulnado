# Documentação do Arquivo `User.java`

## Descrição Geral
A classe `User` é uma representação de um usuário no sistema. Ela contém atributos básicos de identificação e autenticação, além de métodos para geração de tokens JWT, validação de autenticação e busca de usuários no banco de dados.

---

## Estrutura de Dados

### Classe `User`
A classe `User` é uma estrutura de dados que encapsula as seguintes informações sobre um usuário:

| Atributo         | Tipo   | Descrição                                      |
|-------------------|--------|-----------------------------------------------|
| `id`             | String | Identificador único do usuário.               |
| `username`       | String | Nome de usuário.                              |
| `hashedPassword` | String | Senha do usuário armazenada de forma segura.  |

#### Construtor
```java
public User(String id, String username, String hashedPassword)
```
- Inicializa um objeto `User` com os valores fornecidos para `id`, `username` e `hashedPassword`.

---

## Métodos

### `token(String secret)`
Gera um token JWT (JSON Web Token) para o usuário atual.

| Parâmetro | Tipo   | Descrição                                      |
|-----------|--------|-----------------------------------------------|
| `secret`  | String | Chave secreta usada para assinar o token JWT. |

**Retorno:**  
- `String`: O token JWT gerado.

**Funcionamento:**  
1. Converte a chave secreta em um objeto `SecretKey`.
2. Cria um token JWT com o nome de usuário (`username`) como sujeito.
3. Assina o token usando o algoritmo HMAC-SHA e retorna o token gerado.

---

### `assertAuth(String secret, String token)`
Valida a autenticidade de um token JWT.

| Parâmetro | Tipo   | Descrição                                      |
|-----------|--------|-----------------------------------------------|
| `secret`  | String | Chave secreta usada para validar o token JWT. |
| `token`   | String | Token JWT a ser validado.                     |

**Exceções:**  
- Lança uma exceção `Unauthorized` caso o token seja inválido.

**Funcionamento:**  
1. Converte a chave secreta em um objeto `SecretKey`.
2. Usa o `JwtParser` para validar o token JWT.
3. Caso ocorra uma falha na validação, uma exceção é lançada.

---

### `fetch(String un)`
Busca um usuário no banco de dados com base no nome de usuário fornecido.

| Parâmetro | Tipo   | Descrição                                      |
|-----------|--------|-----------------------------------------------|
| `un`      | String | Nome de usuário a ser buscado no banco.       |

**Retorno:**  
- `User`: Um objeto `User` correspondente ao nome de usuário fornecido, ou `null` caso o usuário não seja encontrado.

**Funcionamento:**  
1. Estabelece uma conexão com o banco de dados usando a classe `Postgres`.
2. Executa uma consulta SQL para buscar o usuário com o nome fornecido.
3. Caso o usuário seja encontrado, cria e retorna um objeto `User` com os dados recuperados.
4. Fecha a conexão com o banco de dados.

---

## Insights

### Pontos de Atenção
1. **Vulnerabilidade de Injeção SQL:**  
   O método `fetch` constrói a consulta SQL concatenando diretamente o nome de usuário (`un`). Isso pode expor o sistema a ataques de injeção SQL. Recomenda-se o uso de consultas parametrizadas para evitar esse problema.

2. **Gerenciamento de Conexões:**  
   A conexão com o banco de dados é fechada no método `fetch`, mas o objeto `Statement` (`stmt`) não é explicitamente fechado. Isso pode levar a vazamentos de recursos. É recomendável usar um bloco `try-with-resources` para gerenciar automaticamente os recursos.

3. **Segurança do Token JWT:**  
   A chave secreta usada para assinar e validar tokens JWT é convertida diretamente de uma string. Certifique-se de que a chave seja suficientemente longa e armazenada de forma segura para evitar comprometimentos.

4. **Tratamento de Exceções:**  
   O método `fetch` captura exceções genéricas e imprime o stack trace, mas não fornece um mecanismo robusto para lidar com erros. Considere lançar exceções específicas ou retornar mensagens de erro mais informativas.

### Melhorias Recomendadas
- **Uso de Consultas Parametrizadas:** Substituir a construção manual de consultas SQL por consultas preparadas (`PreparedStatement`) para evitar injeção SQL.
- **Gerenciamento de Recursos:** Adotar o padrão `try-with-resources` para garantir o fechamento adequado de conexões e declarações.
- **Validação de Entrada:** Implementar validações para o parâmetro `un` no método `fetch` para evitar entradas maliciosas.
- **Documentação de Exceções:** Documentar as exceções que podem ser lançadas pelos métodos, especialmente no caso de falhas de autenticação ou erros de banco de dados.
