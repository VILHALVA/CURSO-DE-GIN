# GRAPHQL SERVER WITH THE GIN HTTP FRAMEWORK
Para configurar um servidor GraphQL com o framework HTTP Gin em um projeto Go, siga estes passos:

## 1. Instalação de Pacotes
Certifique-se de ter os pacotes necessários instalados. Para usar o GraphQL com o Gin, você precisará dos seguintes pacotes:

```bash
go get github.com/gin-gonic/gin
go get github.com/graphql-go/graphql
go get github.com/graphql-go/handler
```

## 2. Configuração do Servidor
Aqui está um exemplo básico de como você pode configurar um servidor GraphQL usando o Gin:

```go
package main

import (
    "github.com/gin-gonic/gin"
    "github.com/graphql-go/graphql"
    "github.com/graphql-go/handler"
)

// Definindo o Schema GraphQL
var schema, _ = graphql.NewSchema(graphql.SchemaConfig{
    Query: graphql.NewObject(graphql.ObjectConfig{
        Name: "Query",
        Fields: graphql.Fields{
            "hello": &graphql.Field{
                Type: graphql.String,
                Resolve: func(p graphql.ResolveParams) (interface{}, error) {
                    return "world", nil
                },
            },
        },
    }),
})

func main() {
    // Inicializando o roteador Gin
    r := gin.Default()

    // Configurando o handler GraphQL
    h := handler.New(&handler.Config{
        Schema: &schema,
        Pretty: true,
    })

    // Rota GraphQL
    r.POST("/graphql", func(c *gin.Context) {
        h.ServeHTTP(c.Writer, c.Request)
    })

    // Iniciando o servidor na porta 8080
    r.Run(":8080")
}
```

## 3. Execução do Servidor
Para iniciar o servidor, basta executar o arquivo `main.go`:

```bash
go run main.go
```

## 4. Testando o Servidor GraphQL
Você pode usar ferramentas como o Insomnia ou o Postman para enviar consultas GraphQL para o endpoint `http://localhost:8080/graphql`. Por exemplo, você pode enviar a seguinte consulta:

```graphql
query {
    hello
}
```

Isso deve retornar:

```json
{
    "data": {
        "hello": "world"
    }
}
```

Com esses passos, você configurou com sucesso um servidor GraphQL usando o framework HTTP Gin em seu projeto Go. Você pode expandir o esquema GraphQL e adicionar resolvers para atender às necessidades específicas de sua aplicação.