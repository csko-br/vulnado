# Documentação do Arquivo `Postgres.java`

## Descrição Geral

A classe `Postgres` é responsável por gerenciar a conexão com um banco de dados PostgreSQL, configurar o esquema inicial do banco de dados, inserir dados de exemplo e fornecer utilitários para manipulação de dados. Ela utiliza variáveis de ambiente para configurar a conexão e implementa funcionalidades como hashing de senhas com MD5 e inserção de registros em tabelas.

---

## Estrutura do Código

### Pacote
O código está localizado no pacote:
```java
package com.scalesec.vulnado;
```

### Importações
A classe utiliza as seguintes bibliotecas:
- `java.sql.Connection`, `java.sql.DriverManager`, `java.sql.PreparedStatement`, `java.sql.Statement`: Para manipulação de conexões e execução de comandos SQL.
- `java.math.BigInteger`: Para manipulação de números grandes, usado no cálculo do hash MD5.
- `java.security.MessageDigest`, `java.security.NoSuchAlgorithmException`: Para geração de hash MD5.
- `java.util.UUID`: Para geração de identificadores únicos.

---

## Funcionalidades

### 1. Conexão com o Banco de Dados
O método `connection()` estabelece uma conexão com o banco de dados PostgreSQL utilizando as variáveis de ambiente:
- `PGHOST`: Host do banco de dados.
- `PGDATABASE`: Nome do banco de dados.
- `PGUSER`: Usuário do banco de dados.
- `PGPASSWORD`: Senha do banco de dados.

#### Código:
```java
public static Connection connection()
```

#### Comportamento:
- Carrega o driver PostgreSQL.
- Constrói a URL de conexão.
- Retorna uma instância de `Connection`.

---

### 2. Configuração do Banco de Dados
O método `setup()` cria as tabelas necessárias, limpa dados existentes e insere registros iniciais.

#### Código:
```java
public static void setup()
```

#### Operações:
1. **Criação de Tabelas**:
   - `users`: Armazena informações de usuários.
   - `comments`: Armazena comentários associados a usuários.

2. **Limpeza de Dados**:
   - Remove todos os registros existentes nas tabelas.

3. **Inserção de Dados de Exemplo**:
   - Insere usuários e comentários iniciais.

---

### 3. Hashing de Senhas (MD5)
O método `md5(String input)` gera o hash MD5 de uma string.

#### Código:
```java
public static String md5(String input)
```

#### Comportamento:
- Utiliza a classe `MessageDigest` para calcular o hash MD5.
- Converte o hash para uma representação hexadecimal de 32 caracteres.

---

### 4. Inserção de Usuários
O método `insertUser(String username, String password)` insere um novo usuário na tabela `users`.

#### Código:
```java
private static void insertUser(String username, String password)
```

#### Parâmetros:
- `username`: Nome do usuário.
- `password`: Senha do usuário (será armazenada como hash MD5).

#### Comportamento:
- Gera um `UUID` para o campo `user_id`.
- Insere o registro com a data de criação atual.

---

### 5. Inserção de Comentários
O método `insertComment(String username, String body)` insere um novo comentário na tabela `comments`.

#### Código:
```java
private static void insertComment(String username, String body)
```

#### Parâmetros:
- `username`: Nome do usuário que fez o comentário.
- `body`: Conteúdo do comentário.

#### Comportamento:
- Gera um `UUID` para o campo `id`.
- Insere o registro com a data de criação atual.

---

## Estrutura do Banco de Dados

### Tabela `users`
| Coluna       | Tipo         | Restrições                     |
|--------------|--------------|---------------------------------|
| `user_id`    | `VARCHAR(36)`| Chave primária.                |
| `username`   | `VARCHAR(50)`| Único, não nulo.               |
| `password`   | `VARCHAR(50)`| Não nulo.                      |
| `created_on` | `TIMESTAMP`  | Não nulo.                      |
| `last_login` | `TIMESTAMP`  | Opcional.                      |

### Tabela `comments`
| Coluna       | Tipo         | Restrições                     |
|--------------|--------------|---------------------------------|
| `id`         | `VARCHAR(36)`| Chave primária.                |
| `username`   | `VARCHAR(36)`| Relacionado ao usuário.         |
| `body`       | `VARCHAR(500)`| Não nulo.                     |
| `created_on` | `TIMESTAMP`  | Não nulo.                      |

---

## Insights

1. **Uso de MD5 para Hashing de Senhas**:
   - O MD5 é considerado inseguro para armazenamento de senhas devido à sua vulnerabilidade a ataques de força bruta e colisão. Recomenda-se o uso de algoritmos mais robustos, como bcrypt ou Argon2.

2. **Dependência de Variáveis de Ambiente**:
   - A configuração do banco de dados depende de variáveis de ambiente (`PGHOST`, `PGDATABASE`, `PGUSER`, `PGPASSWORD`). Certifique-se de que essas variáveis estejam configuradas corretamente no ambiente de execução.

3. **Ausência de Tratamento de Erros Detalhado**:
   - Em caso de falha na conexão ou execução de comandos SQL, o código apenas imprime a pilha de erros e encerra o programa. Um tratamento de erros mais robusto seria ideal.

4. **Inserção de Dados Sensíveis**:
   - A senha do usuário "admin" é armazenada diretamente no código. Isso pode ser um risco de segurança. Recomenda-se o uso de um gerenciador de segredos.

5. **Uso de `Statement` para Criação de Tabelas**:
   - Embora seguro neste contexto, o uso de `Statement` pode ser vulnerável a injeções SQL em outros cenários. O uso de `PreparedStatement` é preferível.

6. **Limpeza de Dados Existentes**:
   - O método `setup()` apaga todos os registros das tabelas antes de inserir os dados iniciais. Isso pode não ser adequado em ambientes de produção.

---

## Metadados

- **Nome do Arquivo**: `Postgres.java`
