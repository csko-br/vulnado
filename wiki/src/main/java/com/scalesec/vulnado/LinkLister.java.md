# Documentação: `LinkLister.java`

## Descrição
A classe `LinkLister` é responsável por extrair links de uma página web fornecida. Ela utiliza a biblioteca `Jsoup` para realizar a análise do HTML e oferece métodos para obter os links de forma segura, incluindo validações para evitar o uso de endereços IP privados.

---

## Estrutura da Classe

### Pacote
A classe está localizada no pacote:
```java
package com.scalesec.vulnado;
```

### Importações
A classe utiliza as seguintes bibliotecas:
- **Jsoup**: Para análise e manipulação de HTML.
- **java.util**: Para manipulação de listas.
- **java.io**: Para tratamento de exceções relacionadas a entrada e saída.
- **java.net**: Para manipulação de URLs.

---

## Métodos

### `getLinks(String url)`
Este método realiza a extração de todos os links de uma página web fornecida. Ele utiliza o `Jsoup` para conectar-se à URL e buscar os elementos `<a>` no HTML.

#### Parâmetros
- `url`: Uma string representando a URL da página web.

#### Retorno
- Retorna uma lista de strings (`List<String>`) contendo os links absolutos encontrados na página.

#### Exceções
- `IOException`: Lançada caso ocorra algum erro ao conectar-se à URL ou ao processar o HTML.

#### Lógica
1. Conecta-se à URL fornecida.
2. Analisa o HTML e seleciona todos os elementos `<a>`.
3. Extrai o atributo `href` de cada elemento `<a>` e adiciona à lista de resultados.

---

### `getLinksV2(String url)`
Este método é uma versão aprimorada do `getLinks`, com validações adicionais para evitar o uso de endereços IP privados.

#### Parâmetros
- `url`: Uma string representando a URL da página web.

#### Retorno
- Retorna uma lista de strings (`List<String>`) contendo os links absolutos encontrados na página.

#### Exceções
- `BadRequest`: Lançada caso a URL fornecida seja inválida ou contenha um endereço IP privado.

#### Lógica
1. Cria um objeto `URL` a partir da string fornecida.
2. Obtém o host da URL e verifica se ele corresponde a um endereço IP privado:
   - Endereços que começam com `172.`, `192.168` ou `10.` são considerados privados.
3. Caso o host seja privado, lança uma exceção `BadRequest`.
4. Caso contrário, delega a execução ao método `getLinks`.

---

## Insights

### Segurança
- O método `getLinksV2` adiciona uma camada de segurança ao validar o host da URL, prevenindo o uso de endereços IP privados. Isso é útil para evitar acessos não autorizados a redes internas.

### Dependências
- A biblioteca `Jsoup` é utilizada para análise de HTML. Certifique-se de que ela está incluída no projeto para evitar erros de compilação.

### Exceções Personalizadas
- A classe utiliza uma exceção personalizada chamada `BadRequest`. Certifique-se de que esta classe está definida no projeto, pois ela não está incluída no código fornecido.

### Melhorias Potenciais
- **Validação de URL**: Poderia ser adicionada uma validação mais robusta para URLs inválidas antes de criar o objeto `URL`.
- **Tratamento de Exceções**: O método `getLinksV2` captura todas as exceções genéricas (`Exception`). Seria mais adequado capturar exceções específicas para melhorar a legibilidade e o controle de erros.

---

## Tabela de Métodos

| Método         | Parâmetros       | Retorno            | Exceções         | Descrição                                                                 |
|----------------|------------------|--------------------|------------------|---------------------------------------------------------------------------|
| `getLinks`     | `String url`     | `List<String>`     | `IOException`    | Extrai todos os links absolutos de uma página web.                        |
| `getLinksV2`   | `String url`     | `List<String>`     | `BadRequest`     | Extrai links absolutos, validando o host para evitar endereços IP privados.|
