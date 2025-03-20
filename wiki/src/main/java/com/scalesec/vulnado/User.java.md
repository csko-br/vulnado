# Documentação do Arquivo `User.java`

## Descrição Geral
A classe `User` é uma representação de um usuário no sistema. Ela contém informações básicas do usuário, como `id`, `username` e `hashedPassword`, além de métodos para manipulação de tokens JWT e recuperação de informações do banco de dados.

---

## Estrutura de Dados

### Classe `User`
A classe `User` é uma estrutura de dados que encapsula as seguintes propriedades:

| Propriedade       | Tipo   | Descrição                                      |
|--------------------|--------|-----------------------------------------------|
| `id`              | String | Identificador único do usuário.               |
| `username`        | String | Nome de usuário.                              |
| `hashedPassword`  | String | Senha do usuário armazenada de forma segura.  |

---

## Métodos

### Construtor
```java
public User(String id, String username, String hashedPassword)
```
- **Descrição**: Inicializa uma nova instância da classe `User` com os valores fornecidos.
- **Parâmetros**:
  - `id`: Identificador único do usuário.
  - `username`: Nome de usuário.
  - `hashedPassword`: Senha do usuário armazenada de forma segura.

---

### `token(String secret)`
```java
public String token(String secret)
```
- **Descrição**: Gera um token JWT assinado com uma chave secreta.
- **Parâmetros**:
  - `secret`: Chave secreta usada para assinar o token.
- **Retorno**: Uma string representando o token JWT gerado.

---

### `assertAuth(String secret, String token)`
```java
public static void assertAuth(String secret, String token)
```
- **Descrição**: Valida um token JWT usando uma chave secreta.
- **Parâmetros**:
  - `secret`: Chave secreta usada para validar o token.
  - `token`: Token JWT a ser validado.
- **Exceções**:
  - Lança uma exceção `Unauthorized` caso a validação falhe.

---

### `fetch(String un)`
```java
public static User fetch(String un)
```
- **Descrição**: Recupera um usuário do banco de dados com base no nome de usuário fornecido.
- **Parâmetros**:
  - `un`: Nome de usuário a ser buscado.
- **Retorno**: Uma instância da classe `User` contendo os dados do usuário recuperado ou `null` caso o usuário não seja encontrado.
- **Detalhes de Implementação**:
  - Conexão com o banco de dados é feita utilizando a classe `Postgres`.
  - A consulta SQL é construída de forma insegura, o que pode levar a vulnerabilidades de **SQL Injection**.

---

## Insights

1. **Vulnerabilidade de SQL Injection**:
   - O método `fetch` constrói a consulta SQL concatenando diretamente o nome de usuário (`un`) na string de consulta. Isso pode ser explorado por um atacante para executar comandos SQL maliciosos. Recomenda-se o uso de **Prepared Statements** para evitar essa vulnerabilidade.

2. **Gerenciamento de Exceções**:
   - O método `assertAuth` captura exceções genéricas e lança uma exceção personalizada `Unauthorized`. Isso é útil para abstrair detalhes de implementação, mas pode mascarar informações importantes para depuração.

3. **Segurança do Token JWT**:
   - A chave secreta usada para assinar e validar tokens JWT é derivada diretamente de uma string. É importante garantir que a chave seja suficientemente longa e aleatória para evitar ataques de força bruta.

4. **Conexão com o Banco de Dados**:
   - O método `fetch` não utiliza um bloco `try-with-resources` para gerenciar a conexão com o banco de dados, o que pode levar a vazamentos de recursos caso ocorra uma exceção antes do fechamento da conexão.

5. **Dependências Externas**:
   - A classe utiliza a biblioteca `io.jsonwebtoken` para manipulação de tokens JWT. Certifique-se de que a versão utilizada esteja atualizada para evitar vulnerabilidades conhecidas.

---

## Dependências

| Biblioteca         | Propósito                                      |
|--------------------|-----------------------------------------------|
| `io.jsonwebtoken`  | Manipulação de tokens JWT.                   |
| `javax.crypto`     | Geração de chaves secretas para assinatura.  |
| `java.sql`         | Conexão e manipulação de banco de dados.     |

---

## Melhorias Recomendadas

1. **Uso de Prepared Statements**:
   - Substituir a construção de consultas SQL no método `fetch` por **Prepared Statements** para evitar SQL Injection.

2. **Gerenciamento de Recursos**:
   - Utilizar `try-with-resources` para garantir o fechamento adequado de conexões e outros recursos.

3. **Validação de Entrada**:
   - Implementar validação de entrada para o parâmetro `un` no método `fetch` para evitar entradas maliciosas.

4. **Melhoria na Geração de Tokens**:
   - Adicionar informações adicionais no payload do token JWT, como data de expiração, para melhorar a segurança.

5. **Logging Seguro**:
   - Evitar o uso de `System.out.println` para logging em produção. Utilizar uma biblioteca de logging como `SLF4J` ou `Log4j`.
