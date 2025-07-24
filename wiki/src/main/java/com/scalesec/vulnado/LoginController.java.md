# Documentação: LoginController.java

## Descrição Geral
O arquivo `LoginController.java` implementa um controlador REST para gerenciar autenticação de usuários em uma aplicação Spring Boot. Ele define um endpoint para login, valida as credenciais fornecidas e retorna um token de autenticação em caso de sucesso. Caso as credenciais sejam inválidas, uma exceção personalizada é lançada.

---

## Estrutura do Código

### Classes e Componentes

| Classe/Componente       | Descrição                                                                 |
|--------------------------|---------------------------------------------------------------------------|
| `LoginController`        | Controlador REST que gerencia o endpoint de login.                       |
| `LoginRequest`           | Classe de modelo que representa a requisição de login.                   |
| `LoginResponse`          | Classe de modelo que encapsula a resposta de login com o token gerado.   |
| `Unauthorized`           | Exceção personalizada para tratar acessos não autorizados.               |

---

### Endpoints

| Método HTTP | Endpoint | Consumo (Content-Type) | Produção (Content-Type) | Descrição                                                                 |
|-------------|----------|------------------------|--------------------------|---------------------------------------------------------------------------|
| `POST`      | `/login` | `application/json`     | `application/json`       | Valida as credenciais do usuário e retorna um token de autenticação.      |

---

## Detalhamento das Classes

### LoginController
- **Anotações**:
  - `@RestController`: Define que esta classe é um controlador REST.
  - `@EnableAutoConfiguration`: Habilita a configuração automática do Spring Boot.
  - `@CrossOrigin(origins = "*")`: Permite requisições de qualquer origem (CORS).
  - `@RequestMapping`: Configura o endpoint `/login` para aceitar requisições POST com JSON.

- **Atributos**:
  - `secret`: Uma string que representa o segredo da aplicação, configurado via propriedades (`@Value("${app.secret}")`).

- **Método**:
  - `login(LoginRequest input)`: 
    - Recebe um objeto `LoginRequest` contendo `username` e `password`.
    - Busca o usuário pelo nome de usuário (`User.fetch(input.username)`).
    - Compara a senha fornecida com a senha armazenada no banco de dados (hash MD5).
    - Retorna um objeto `LoginResponse` com o token gerado ou lança uma exceção `Unauthorized` em caso de falha.

---

### LoginRequest
- **Descrição**: Representa os dados enviados na requisição de login.
- **Atributos**:
  - `username`: Nome de usuário.
  - `password`: Senha do usuário.

---

### LoginResponse
- **Descrição**: Representa a resposta do endpoint de login.
- **Atributos**:
  - `token`: Token de autenticação gerado.
- **Construtor**:
  - `LoginResponse(String msg)`: Inicializa o objeto com o token gerado.

---

### Unauthorized
- **Descrição**: Exceção personalizada para tratar acessos não autorizados.
- **Anotações**:
  - `@ResponseStatus(HttpStatus.UNAUTHORIZED)`: Define o status HTTP 401 para esta exceção.
- **Construtor**:
  - `Unauthorized(String exception)`: Inicializa a exceção com uma mensagem personalizada.

---

## Insights

1. **Segurança**:
   - O uso de hash MD5 para senhas não é considerado seguro. Recomenda-se utilizar algoritmos mais robustos como bcrypt ou Argon2.
   - O segredo da aplicação (`secret`) é armazenado em variáveis de ambiente, o que é uma boa prática. Certifique-se de que ele esteja protegido contra acessos não autorizados.

2. **CORS**:
   - A configuração `@CrossOrigin(origins = "*")` permite requisições de qualquer origem. Isso pode ser um risco de segurança em aplicações que não precisam de acesso global. Considere restringir as origens permitidas.

3. **Tratamento de Exceções**:
   - A classe `Unauthorized` é bem implementada para lidar com acessos não autorizados, retornando o status HTTP apropriado.

4. **Serialização**:
   - As classes `LoginRequest` e `LoginResponse` implementam `Serializable`, o que facilita a conversão de objetos para JSON e vice-versa.

5. **Dependências Externas**:
   - A classe `User` e o método `Postgres.md5()` não estão definidos no código fornecido. Certifique-se de que essas dependências estejam corretamente implementadas e seguras.

---

## Referências
- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
