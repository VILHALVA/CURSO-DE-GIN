# REST API DOCUMENTATION AND TEST WITH SWAGGER - OPENAPI
Para documentar e testar uma API REST com Swagger (OpenAPI) em um projeto Go, você pode seguir estes passos:

## 1. Instalação do Pacote Swagger
Para usar o Swagger em um projeto Go, você precisa instalar o pacote `swaggo/swag`. Você pode instalar usando o seguinte comando:

```bash
go get -u github.com/swaggo/swag/cmd/swag
```

## 2. Adicionar Anotações ao Código
Você precisará adicionar anotações especiais ao seu código para documentar suas rotas. Por exemplo:

```go
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
    "github.com/swaggo/gin-swagger"
    "github.com/swaggo/gin-swagger/swaggerFiles"
    "github.com/swaggo/swag"
)

// @title Your API Title
// @version 1.0
// @description This is a sample API
// @BasePath /api/v1
func main() {
    r := gin.Default()

    // Rotas CRUD
    // @Summary Get a list of books
    // @Description Get all books
    // @ID get-books
    // @Produce json
    // @Success 200 {object} []Book
    // @Router /books [get]
    r.GET("/books", getBooks)

    // Rotas de Documentação Swagger
    r.GET("/swagger/*any", ginSwagger.WrapHandler(swaggerFiles.Handler))

    r.Run(":8080")
}

// Book struct
type Book struct {
    ID     int    `json:"id"`
    Title  string `json:"title"`
    Author string `json:"author"`
}

// @Summary Get a list of books
// @Description Get all books
// @Produce json
// @Success 200 {object} []Book
// @Router /books [get]
func getBooks(c *gin.Context) {
    books := []Book{
        {ID: 1, Title: "1984", Author: "George Orwell"},
        {ID: 2, Title: "Brave New World", Author: "Aldous Huxley"},
    }
    c.JSON(http.StatusOK, books)
}

func init() {
    // Inicialize o Swagger para o pacote principal
    swag.Register(swag.Name, nil)
}
```

## 3. Gerar Documentação
Depois de adicionar as anotações ao seu código, você pode usar o comando `swag init` para gerar os arquivos de documentação:

```bash
swag init
```

Este comando irá gerar um diretório chamado `docs` com os arquivos de documentação Swagger.

## 4. Executar o Servidor
Agora, você pode executar o servidor e acessar a documentação Swagger em `http://localhost:8080/swagger/index.html`.

## 5. Testar a API
Você pode usar a interface Swagger para testar sua API diretamente na documentação Swagger. Isso permite que você envie solicitações HTTP para suas rotas e veja as respostas correspondentes.

Com esses passos, você pode documentar e testar sua API REST com Swagger em um projeto Go, facilitando o desenvolvimento e a interação com sua API. Certifique-se de atualizar suas anotações conforme necessário para refletir as mudanças em suas rotas.