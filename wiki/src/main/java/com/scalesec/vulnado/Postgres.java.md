# Documentação do Arquivo `Postgres.java`

## Visão Geral
O arquivo `Postgres.java` implementa uma classe que gerencia a conexão com um banco de dados PostgreSQL, realiza a configuração inicial do banco de dados, insere dados de exemplo e fornece utilitários para manipulação de dados. Ele também inclui uma função para calcular o hash MD5 de uma string.

---

## Estrutura da Classe

### Pacote
A classe pertence ao pacote `com.scalesec.vulnado`.

### Importações
A classe utiliza as seguintes bibliotecas:
- **`java.sql.Connection`**: Para gerenciar conexões com o banco de dados.
- **`java.sql.DriverManager`**: Para obter conexões JDBC.
- **`java.sql.PreparedStatement` e `java.sql.Statement`**: Para executar comandos SQL.
- **`java.math.BigInteger`**: Para manipulação de números grandes.
- **`java.security.MessageDigest` e `java.security.NoSuchAlgorithmException`**: Para cálculo de hash MD5.
- **`java.util.UUID`**: Para geração de identificadores únicos.

---

## Métodos

### `connection()`
Estabelece uma conexão com o banco de dados PostgreSQL utilizando variáveis de ambiente para configurar o host, banco de dados, usuário e senha.

- **Entrada**: Nenhuma.
- **Saída**: Objeto `Connection` representando a conexão com o banco de dados.
- **Tratamento de Erros**: Imprime o erro no console e encerra o programa em caso de falha.

---

### `setup()`
Configura o banco de dados criando tabelas, limpando dados existentes e inserindo dados iniciais.

- **Tabelas Criadas**:
  - `users`: Contém informações de usuários.
  - `comments`: Contém comentários associados a usuários.
- **Dados Inseridos**:
  - Usuários: `admin`, `alice`, `bob`, `eve`, `rick`.
  - Comentários: `"cool dog m8"` (por `rick`), `"OMG so cute!"` (por `alice`).

---

### `md5(String input)`
Calcula o hash MD5 de uma string.

- **Entrada**: Uma string (`input`).
- **Saída**: Hash MD5 da string em formato hexadecimal.
- **Tratamento de Erros**: Lança uma exceção `RuntimeException` em caso de falha.

---

### `insertUser(String username, String password)`
Insere um novo usuário na tabela `users`.

- **Entrada**:
  - `username`: Nome do usuário.
  - `password`: Senha do usuário (armazenada como hash MD5).
- **Saída**: Nenhuma.
- **Tratamento de Erros**: Imprime o erro no console em caso de falha.

---

### `insertComment(String username, String body)`
Insere um novo comentário na tabela `comments`.

- **Entrada**:
  - `username`: Nome do usuário que fez o comentário.
  - `body`: Conteúdo do comentário.
- **Saída**: Nenhuma.
- **Tratamento de Erros**: Imprime o erro no console em caso de falha.

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
| `body`       | `VARCHAR(500)`| Conteúdo do comentário.        |
| `created_on` | `TIMESTAMP`  | Não nulo.                      |

---

## Insights

1. **Segurança**:
   - A senha dos usuários é armazenada como hash MD5. No entanto, o MD5 é considerado inseguro para armazenamento de senhas devido à sua vulnerabilidade a ataques de força bruta e colisão. Recomenda-se o uso de algoritmos mais robustos, como bcrypt ou Argon2.
   - O uso de variáveis de ambiente para credenciais do banco de dados é uma boa prática, mas deve ser complementado com medidas adicionais, como o uso de gerenciadores de segredos.

2. **Manutenção**:
   - O método `setup()` limpa todos os dados existentes antes de inserir os dados iniciais. Isso pode ser problemático em ambientes de produção.
   - Não há validação de entrada nos métodos `insertUser` e `insertComment`, o que pode levar a problemas como injeção de SQL, mesmo com o uso de `PreparedStatement`.

3. **Escalabilidade**:
   - A classe não utiliza um pool de conexões, o que pode impactar o desempenho em sistemas com alta carga.

4. **Boas Práticas**:
   - A geração de UUIDs para identificadores primários é uma prática recomendada para garantir unicidade.
   - O uso de `PreparedStatement` ajuda a prevenir injeção de SQL.

5. **Melhorias Potenciais**:
   - Implementar logs estruturados em vez de usar `System.out.println` e `e.printStackTrace`.
   - Adicionar suporte a transações para garantir consistência em operações críticas.
