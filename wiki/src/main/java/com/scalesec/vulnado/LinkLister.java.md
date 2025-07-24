# Documentação do Arquivo `LinkLister.java`

## Descrição Geral

A classe `LinkLister` é responsável por extrair links de uma página web fornecida por meio de uma URL. Ela utiliza a biblioteca [JSoup](https://jsoup.org/) para realizar a análise do HTML e identificar os elementos `<a>` que representam links. Além disso, a classe implementa uma validação para evitar o uso de URLs que apontem para endereços IP privados.

---

## Estrutura do Código

### Pacote
O código está localizado no pacote:
```java
package com.scalesec.vulnado;
```

### Importações
O código utiliza as seguintes bibliotecas e classes:
- **JSoup**: Para realizar a análise e manipulação de documentos HTML.
- **java.util.List e java.util.ArrayList**: Para armazenar e manipular listas de links.
- **java.io.IOException**: Para tratar exceções relacionadas a operações de entrada/saída.
- **java.net.URL**: Para manipular URLs e extrair informações como o host.

---

## Métodos

### `getLinks(String url)`
Este método realiza a extração de todos os links de uma página web.

#### Parâmetros
- `url` (String): A URL da página web de onde os links serão extraídos.

#### Retorno
- `List<String>`: Uma lista contendo os links absolutos encontrados na página.

#### Funcionamento
1. Conecta-se à URL fornecida utilizando o método `Jsoup.connect(url).get()`.
2. Seleciona todos os elementos `<a>` da página.
3. Para cada elemento `<a>`, extrai o link absoluto usando `link.absUrl("href")` e o adiciona à lista de resultados.
4. Retorna a lista de links.

#### Exceções
- Lança `IOException` caso ocorra algum problema ao conectar-se à URL.

---

### `getLinksV2(String url)`
Este método é uma versão aprimorada de `getLinks`, com validação adicional para evitar o uso de URLs que apontem para endereços IP privados.

#### Parâmetros
- `url` (String): A URL da página web de onde os links serão extraídos.

#### Retorno
- `List<String>`: Uma lista contendo os links absolutos encontrados na página.

#### Funcionamento
1. Converte a URL fornecida em um objeto `URL` para extrair o host.
2. Verifica se o host começa com um dos seguintes prefixos de IP privado:
   - `172.`
   - `192.168`
   - `10.`
3. Caso o host seja um IP privado, lança uma exceção `BadRequest` com a mensagem "Use of Private IP".
4. Caso contrário, delega a extração de links ao método `getLinks`.

#### Exceções
- Lança `BadRequest` caso:
  - O host seja um IP privado.
  - Ocorra qualquer outra exceção durante a execução.

---

## Classes e Exceções Relacionadas

### `BadRequest`
Embora não esteja definida no código fornecido, a exceção `BadRequest` é utilizada para sinalizar erros relacionados à validação de URLs. Presume-se que seja uma classe personalizada definida em outro local do projeto.

---

## Insights

1. **Validação de Segurança**: O método `getLinksV2` implementa uma validação importante para evitar o uso de URLs que apontem para redes privadas, o que pode ser uma medida de segurança para evitar ataques SSRF (Server-Side Request Forgery).

2. **Uso de JSoup**: A biblioteca JSoup é amplamente utilizada para análise de HTML e é uma escolha eficiente para extrair links de páginas web.

3. **Tratamento de Exceções**: O método `getLinksV2` encapsula todas as exceções em uma única exceção `BadRequest`, o que pode ser útil para padronizar o tratamento de erros no restante do sistema.

4. **Possível Melhoria**: O método `getLinksV2` poderia ser expandido para validar outros tipos de URLs potencialmente perigosas, como aquelas que utilizam esquemas não confiáveis (ex.: `file://`).

5. **Dependência Externa**: A funcionalidade depende da conectividade com a internet e da acessibilidade da URL fornecida, o que pode ser um ponto de falha em ambientes restritos.

---

## Tabela de Métodos

| Método            | Descrição                                                                 | Parâmetros         | Retorno         | Exceções         |
|-------------------|---------------------------------------------------------------------------|--------------------|-----------------|-----------------|
| `getLinks`        | Extrai todos os links de uma página web.                                 | `url` (String)     | `List<String>`  | `IOException`   |
| `getLinksV2`      | Versão aprimorada de `getLinks` com validação de IP privado.             | `url` (String)     | `List<String>`  | `BadRequest`    |
