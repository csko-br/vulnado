# Documentação Técnica: LoginController.java

## Descrição Geral
O arquivo `LoginController.java` implementa um controlador REST para gerenciar autenticação de usuários em uma aplicação Spring Boot. Ele define um endpoint para login, valida as credenciais fornecidas e retorna um token de autenticação em caso de sucesso. Caso as credenciais sejam inválidas, uma exceção personalizada é lançada.

---

## Estrutura do Código

### Pacote
O código está localizado no pacote `com.scalesec.vulnado`.

### Importações
O código utiliza as seguintes bibliotecas e classes:
- **Spring Boot**: Para configuração e inicialização da aplicação.
- **Spring Web**: Para criar endpoints REST e gerenciar requisições HTTP.
- **Java Beans**: Para injeção de dependências e manipulação de objetos.
- **Serializable**: Para serialização de objetos.

---

## Classes e Componentes

### 1. **LoginController**
- **Descrição**: Classe principal que define o endpoint `/login` para autenticação de usuários.
- **Anotações**:
  - `@RestController`: Indica que a classe é um controlador REST.
  - `@EnableAutoConfiguration`: Habilita a configuração automática do Spring Boot.
  - `@Value("${app.secret}")`: Injeta o valor da propriedade `app.secret` do arquivo de configuração.
  - `@CrossOrigin(origins = "*")`: Permite requisições de qualquer origem (CORS).
  - `@RequestMapping`: Define o endpoint `/login` com método HTTP POST, consumindo e produzindo JSON.
- **Método**:
  - `login(LoginRequest input)`: 
    - Recebe um objeto `LoginRequest` contendo `username` e `password`.
    - Busca o usuário pelo nome de usuário utilizando o método estático `User.fetch()`.
    - Compara o hash da senha fornecida com o hash armazenado no banco de dados.
    - Retorna um objeto `LoginResponse` contendo o token gerado, ou lança uma exceção `Unauthorized` em caso de falha.

---

### 2. **LoginRequest**
- **Descrição**: Classe que representa o corpo da requisição de login.
- **Atributos**:
  - `username`: Nome de usuário fornecido pelo cliente.
  - `password`: Senha fornecida pelo cliente.
- **Implementação**:
  - Implementa a interface `Serializable` para permitir a serialização.

---

### 3. **LoginResponse**
- **Descrição**: Classe que encapsula a resposta de autenticação.
- **Atributos**:
  - `token`: Token de autenticação gerado para o usuário.
- **Construtor**:
  - `LoginResponse(String msg)`: Inicializa o objeto com o token fornecido.
- **Implementação**:
  - Implementa a interface `Serializable`.

---

### 4. **Unauthorized**
- **Descrição**: Classe de exceção personalizada para tratar falhas de autenticação.
- **Anotações**:
  - `@ResponseStatus(HttpStatus.UNAUTHORIZED)`: Define o código de status HTTP como 401 (Unauthorized).
- **Construtor**:
  - `Unauthorized(String exception)`: Inicializa a exceção com uma mensagem personalizada.

---

## Endpoints

| Método HTTP | URL         | Consome         | Produz          | Descrição                                                                 |
|-------------|-------------|-----------------|-----------------|---------------------------------------------------------------------------|
| POST        | `/login`    | `application/json` | `application/json` | Autentica o usuário e retorna um token de acesso em caso de sucesso.     |

---

## Insights

1. **Segurança**:
   - O uso de `@CrossOrigin(origins = "*")` permite requisições de qualquer origem, o que pode ser um risco de segurança em produção. É recomendável restringir as origens permitidas.
   - A comparação de hashes de senha é feita diretamente no código. Certifique-se de que o método `Postgres.md5()` seja seguro e que o armazenamento de senhas no banco de dados esteja devidamente protegido.

2. **Tratamento de Erros**:
   - A exceção personalizada `Unauthorized` melhora a clareza do código e facilita o tratamento de erros no cliente.

3. **Melhorias Potenciais**:
   - Adicionar logs para monitorar tentativas de login.
   - Implementar limites de tentativas de login para evitar ataques de força bruta.
   - Utilizar tokens com expiração e renovação para maior segurança.

4. **Serialização**:
   - As classes `LoginRequest` e `LoginResponse` implementam `Serializable`, o que é útil para transmissão de dados, mas pode ser desnecessário se não houver necessidade de persistência ou transporte além do contexto HTTP.

5. **Configuração Externa**:
   - O segredo (`app.secret`) é injetado via configuração externa, o que é uma boa prática para separar dados sensíveis do código.

---

## Dependências Externas
- **User.fetch()**: Método estático para buscar informações do usuário. Não está definido no código fornecido, mas é essencial para o funcionamento do login.
- **Postgres.md5()**: Método para gerar o hash da senha. Certifique-se de que ele utiliza algoritmos seguros e atualizados.
