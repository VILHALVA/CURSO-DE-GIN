# MIDDLEWARES 101 - AUTHORIZATION, LOGGING AND DEBUGGING
Eles permitem executar código comum em várias rotas ou grupos de rotas. Vamos explorar alguns exemplos básicos de middlewares para autorização, registro e depuração.

## 1. Middleware de Autorização
Um middleware de autorização pode ser usado para verificar se o usuário tem permissão para acessar determinada rota. Aqui está um exemplo de como você pode implementar um middleware de autorização simples:

```go
func AuthMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        // Verificar se o usuário está autenticado
        if userIsAuthenticated(c) {
            // Se o usuário estiver autenticado, passe para o próximo middleware
            c.Next()
        } else {
            // Caso contrário, retorne um erro de não autorizado
            c.JSON(http.StatusUnauthorized, gin.H{"error": "Unauthorized"})
            c.Abort() // Interrompe a execução subsequente de middlewares e handlers
        }
    }
}

func userIsAuthenticated(c *gin.Context) bool {
    // Verifica se o usuário está autenticado (implementação fictícia)
    // Aqui você pode verificar se o token é válido, se o usuário tem as permissões adequadas, etc.
    // Retorna verdadeiro se o usuário estiver autenticado, falso caso contrário
    return true
}
```

Para usar este middleware em suas rotas, basta adicionar `AuthMiddleware()` antes do manipulador de rota que requer autorização:

```go
r.GET("/protected", AuthMiddleware(), func(c *gin.Context) {
    // Handler para rota protegida
    c.JSON(http.StatusOK, gin.H{"message": "You are authorized"})
})
```

## 2. Middleware de Registro
Um middleware de registro pode ser usado para registrar informações sobre cada requisição que chega ao servidor. Aqui está um exemplo de como você pode implementar um middleware de registro simples:

```go
func LoggerMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        // Registro de informações sobre a requisição
        log.Println("Request:", c.Request.Method, c.Request.URL.Path)

        // Passar para o próximo middleware
        c.Next()
    }
}
```

Adicione este middleware a todas as rotas para registrar informações sobre cada requisição:

```go
r.Use(LoggerMiddleware())
```

## 3. Middleware de Depuração
Um middleware de depuração pode ser útil durante o desenvolvimento para visualizar informações sobre o estado da aplicação, como o corpo da requisição, os parâmetros da URL, etc. Aqui está um exemplo de como você pode implementar um middleware de depuração:

```go
func DebugMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        // Exibir informações sobre a requisição
        log.Println("Request Body:", c.Request.Body)
        log.Println("Query Params:", c.Request.URL.Query())

        // Passar para o próximo middleware
        c.Next()
    }
}
```

Adicione este middleware a todas as rotas para depurar informações sobre cada requisição:

```go
r.Use(DebugMiddleware())
```

Estes são apenas alguns exemplos básicos de middlewares para autorização, registro e depuração. Você pode personalizá-los de acordo com as necessidades específicas do seu aplicativo. Lembre-se sempre de testar e validar seus middlewares para garantir o comportamento desejado.