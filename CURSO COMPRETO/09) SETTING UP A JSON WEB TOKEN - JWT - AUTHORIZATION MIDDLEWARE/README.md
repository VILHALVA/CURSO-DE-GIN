# SETTING UP A JSON WEB TOKEN - JWT - AUTHORIZATION MIDDLEWARE
Configurar um middleware de autorização JSON Web Token (JWT) é uma etapa importante ao construir aplicativos web para garantir que rotas protegidas estejam acessíveis apenas para usuários autenticados. Aqui está um guia passo a passo para configurar um middleware de autorização JWT em um servidor HTTP usando a linguagem Go e o framework Gin:

## 1. Instalar Dependências
Certifique-se de ter o pacote `github.com/dgrijalva/jwt-go` instalado. Se não estiver instalado, você pode instalá-lo usando o seguinte comando:

```bash
go get -u github.com/dgrijalva/jwt-go
```

## 2. Configurar o Middleware JWT
Crie uma função middleware que verifica e decodifica o token JWT na solicitação HTTP. Aqui está um exemplo de como você pode fazer isso em Go:

```go
package main

import (
    "net/http"
    "strings"

    "github.com/dgrijalva/jwt-go"
    "github.com/gin-gonic/gin"
)

func JWTMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        authHeader := c.GetHeader("Authorization")
        if authHeader == "" {
            c.JSON(http.StatusUnauthorized, gin.H{"error": "Authorization header is missing"})
            c.Abort()
            return
        }

        tokenString := strings.Split(authHeader, " ")[1]
        token, err := jwt.Parse(tokenString, func(token *jwt.Token) (interface{}, error) {
            return []byte("your-secret-key"), nil // Substitua "your-secret-key" pela sua chave secreta JWT
        })

        if err != nil || !token.Valid {
            c.JSON(http.StatusUnauthorized, gin.H{"error": "Unauthorized"})
            c.Abort()
            return
        }

        c.Next()
    }
}
```

## 3. Usar o Middleware nas Rotas Protegidas
Agora, você pode usar o middleware JWT nas rotas que deseja proteger. Aqui está um exemplo de como fazer isso:

```go
func main() {
    r := gin.Default()

    // Rota pública
    r.GET("/public", func(c *gin.Context) {
        c.JSON(http.StatusOK, gin.H{"message": "This is a public route"})
    })

    // Rota protegida
    r.GET("/protected", JWTMiddleware(), func(c *gin.Context) {
        c.JSON(http.StatusOK, gin.H{"message": "This is a protected route"})
    })

    r.Run(":8080")
}
```

## 4. Gerar Tokens JWT
Você também precisará de um método para gerar tokens JWT ao autenticar usuários. Aqui está um exemplo de como você pode fazer isso:

```go
func generateToken() (string, error) {
    token := jwt.New(jwt.SigningMethodHS256)
    claims := token.Claims.(jwt.MapClaims)
    claims["authorized"] = true
    tokenString, err := token.SignedString([]byte("your-secret-key")) // Substitua "your-secret-key" pela sua chave secreta JWT
    if err != nil {
        return "", err
    }
    return tokenString, nil
}
```

## 5. Testar o Middleware
Com tudo configurado, você pode testar o middleware JWT. Certifique-se de incluir o token JWT no cabeçalho `Authorization` ao fazer solicitações para rotas protegidas.

Com este guia, você configurou com sucesso um middleware de autorização JWT em um servidor HTTP usando Go e Gin. Certifique-se de ajustar as configurações e as chaves conforme necessário para atender aos requisitos específicos do seu aplicativo.