# HOW TO SETUP JWT AUTHORIZATION FOR A GRAPHQL SERVER
Para configurar a autorização JWT para um servidor GraphQL, você geralmente seguirá estes passos:

## 1. Instalar Pacotes Necessários
Certifique-se de ter os pacotes necessários instalados para lidar com autenticação e autorização JWT. Você pode usar pacotes como `github.com/dgrijalva/jwt-go` para manipulação de JWT.

```bash
go get github.com/dgrijalva/jwt-go
```

## 2. Implementação do Middleware
Implemente uma função de middleware para lidar com a autorização JWT. Este middleware deve interceptar as solicitações recebidas, extrair o token JWT do cabeçalho da solicitação, verificar sua autenticidade e validade, e em seguida, anexar as informações de usuário autenticado ao contexto da solicitação.

Aqui está um exemplo de como você pode implementar o middleware JWT:

```go
package middleware

import (
    "net/http"
    "strings"

    "github.com/dgrijalva/jwt-go"
    "github.com/gin-gonic/gin"
)

func JWTMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        // Extrair token JWT do cabeçalho Authorization
        authHeader := c.GetHeader("Authorization")
        if authHeader == "" {
            c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"error": "Cabeçalho de autorização está faltando"})
            return
        }
        tokenString := strings.Split(authHeader, " ")[1]

        // Validar token JWT
        token, err := jwt.Parse(tokenString, func(token *jwt.Token) (interface{}, error) {
            // TODO: Substitua pela sua chave secreta JWT
            return []byte("sua_chave_secreta"), nil
        })
        if err != nil || !token.Valid {
            c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"error": "Não autorizado"})
            return
        }

        // Anexar informações do usuário ao contexto da solicitação
        claims, _ := token.Claims.(jwt.MapClaims)
        c.Set("user", claims)

        c.Next()
    }
}
```

## 3. Aplicar Middleware ao Endpoint GraphQL
Aplique o middleware JWT ao endpoint onde seu servidor GraphQL está hospedado. Este middleware deve ser colocado antes do seu manipulador GraphQL para garantir que seja executado antes de processar as consultas GraphQL.

```go
package main

import (
    "github.com/gin-gonic/gin"
    "github.com/graphql-go/graphql"
    "github.com/graphql-go/handler"
    "seuapp/middleware"
)

// Defina o seu esquema GraphQL
var schema, _ = graphql.NewSchema(/* configuração do esquema */)

func main() {
    r := gin.Default()

    // Aplicar o middleware JWT ao endpoint GraphQL
    r.POST("/graphql", middleware.JWTMiddleware(), func(c *gin.Context) {
        h := handler.New(&handler.Config{
            Schema: &schema,
            // Outras configurações do manipulador
        })
        h.ServeHTTP(c.Writer, c.Request)
    })

    // Executar o servidor Gin
    r.Run(":8080")
}
```

## 4. Acessar Informações do Usuário nos Resolvers
Nos resolvers do seu GraphQL, você pode acessar as informações do usuário anexadas ao contexto da solicitação pelo middleware JWT. Essas informações geralmente incluem detalhes como o ID do usuário, funções ou quaisquer reivindicações personalizadas que você inclua na carga útil JWT. Use essas informações para implementar lógica de autorização dentro dos seus resolvers.

Seguindo esses passos, você pode configurar a autorização JWT para o seu servidor GraphQL, garantindo que apenas usuários autenticados e autorizados possam acessar sua API GraphQL.