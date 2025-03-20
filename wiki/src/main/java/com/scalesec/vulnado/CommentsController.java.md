# Documentação do Arquivo `CommentsController.java`

## Visão Geral
Este arquivo contém a implementação de um controlador REST em Java utilizando o framework Spring Boot. O controlador gerencia operações relacionadas a comentários, incluindo listagem, criação e exclusão. Ele também define classes auxiliares para lidar com requisições e exceções personalizadas.

---

## Estrutura do Código

### Classes e Componentes

| Classe/Componente       | Descrição                                                                 |
|-------------------------|---------------------------------------------------------------------------|
| `CommentsController`    | Controlador REST principal que gerencia as operações relacionadas a comentários. |
| `CommentRequest`        | Classe auxiliar que representa o corpo da requisição para criação de comentários. |
| `BadRequest`            | Exceção personalizada para erros de requisição com status HTTP 400.      |
| `ServerError`           | Exceção personalizada para erros internos do servidor com status HTTP 500. |

---

## Endpoints

### 1. **Listar Comentários**
- **URL:** `/comments`
- **Método HTTP:** `GET`
- **Headers:**
  - `x-auth-token` (String): Token de autenticação.
- **Resposta:**
  - Retorna uma lista de objetos `Comment` no formato JSON.
- **Descrição:**
  - Este endpoint lista todos os comentários disponíveis. Ele valida o token de autenticação antes de retornar os dados.

---

### 2. **Criar Comentário**
- **URL:** `/comments`
- **Método HTTP:** `POST`
- **Headers:**
  - `x-auth-token` (String): Token de autenticação.
- **Body (JSON):**
  ```json
  {
    "username": "string",
    "body": "string"
  }
  ```
- **Resposta:**
  - Retorna o objeto `Comment` criado no formato JSON.
- **Descrição:**
  - Este endpoint cria um novo comentário com base nos dados fornecidos no corpo da requisição.

---

### 3. **Excluir Comentário**
- **URL:** `/comments/{id}`
- **Método HTTP:** `DELETE`
- **Headers:**
  - `x-auth-token` (String): Token de autenticação.
- **Path Variables:**
  - `id` (String): Identificador do comentário a ser excluído.
- **Resposta:**
  - Retorna um valor booleano indicando o sucesso ou falha da operação.
- **Descrição:**
  - Este endpoint exclui um comentário específico com base no ID fornecido.

---

## Classes Auxiliares

### 1. **`CommentRequest`**
- **Descrição:** Representa o corpo da requisição para criação de um comentário.
- **Atributos:**
  | Atributo  | Tipo   | Descrição                     |
  |-----------|--------|-------------------------------|
  | `username`| String | Nome do usuário que comentou. |
  | `body`    | String | Conteúdo do comentário.       |

---

### 2. **Exceções Personalizadas**

| Classe       | Código HTTP | Descrição                                                                 |
|--------------|-------------|---------------------------------------------------------------------------|
| `BadRequest` | 400         | Lançada quando há um erro na requisição do cliente.                       |
| `ServerError`| 500         | Lançada quando ocorre um erro interno no servidor.                        |

---

## Insights

1. **Autenticação:** 
   - Todos os endpoints exigem um token de autenticação (`x-auth-token`) para validar o acesso. A validação é feita por meio do método `User.assertAuth`.

2. **CORS:** 
   - O controlador permite requisições de qualquer origem devido à anotação `@CrossOrigin(origins = "*")`.

3. **Gerenciamento de Exceções:**
   - Exceções personalizadas (`BadRequest` e `ServerError`) são utilizadas para melhorar a clareza e o tratamento de erros.

4. **Dependência de Classes Externas:**
   - O código depende de métodos externos como `Comment.fetch_all`, `Comment.create` e `Comment.delete`, que não estão definidos neste arquivo.

5. **Configuração Externa:**
   - O valor de `secret` é injetado a partir de uma propriedade externa (`app.secret`), indicando a necessidade de configuração no ambiente de execução.

6. **Serialização:**
   - A classe `CommentRequest` implementa `Serializable`, o que pode ser útil para persistência ou transmissão de dados.

---
