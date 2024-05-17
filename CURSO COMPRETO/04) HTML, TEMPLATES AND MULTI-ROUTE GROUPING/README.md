# HTML, TEMPLATES AND MULTI-ROUTE GROUPING
Trabalhar com HTML e templates no Gin é bastante simples e eficiente. O Gin oferece suporte nativo para renderização de templates HTML usando a biblioteca `html/template` do Go. Além disso, o framework permite agrupar rotas de maneira organizada usando a funcionalidade de Multi-Route Grouping.

## Renderização de Templates HTML
Para renderizar templates HTML no Gin, primeiro precisamos configurar o diretório onde estão armazenados os arquivos de template. Em seguida, podemos usar a função `gin.Default()` para criar uma instância do Gin e chamar o método `LoadHTMLGlob()` para carregar os templates.

### Exemplo de Configuração e Renderização de Templates HTML:
```go
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    // Cria uma instância do Gin
    r := gin.Default()

    // Define o diretório onde estão armazenados os arquivos de template
    r.LoadHTMLGlob("templates/*")

    // Define uma rota para renderizar o template index.html
    r.GET("/", func(c *gin.Context) {
        // Renderiza o template index.html
        c.HTML(http.StatusOK, "index.html", gin.H{
            "title": "Página Inicial",
        })
    })

    // Inicia o servidor na porta 8080
    r.Run(":8080")
}
```

No exemplo acima, supomos que os arquivos de template estejam armazenados no diretório `templates` na raiz do projeto. O método `LoadHTMLGlob()` carrega todos os arquivos de template com a extensão `.html` no diretório especificado.

## Multi-Route Grouping
Multi-Route Grouping no Gin permite agrupar rotas relacionadas para facilitar a organização do código. Isso é útil quando há várias rotas que compartilham um prefixo comum ou precisam aplicar middleware em conjunto.

### Exemplo de Multi-Route Grouping:
```go
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    // Cria uma instância do Gin
    r := gin.Default()

    // Define um grupo de rotas com o prefixo /api
    api := r.Group("/api")
    {
        // Define uma rota dentro do grupo /api
        api.GET("/users", func(c *gin.Context) {
            c.JSON(http.StatusOK, gin.H{"message": "Lista de usuários"})
        })
        
        // Define outra rota dentro do grupo /api
        api.GET("/products", func(c *gin.Context) {
            c.JSON(http.StatusOK, gin.H{"message": "Lista de produtos"})
        })
    }

    // Inicia o servidor na porta 8080
    r.Run(":8080")
}
```

Neste exemplo, todas as rotas definidas dentro do grupo `/api` terão o prefixo `/api` adicionado a elas. Isso ajuda a manter o código organizado e legível, especialmente em aplicativos com muitas rotas.

Com esses recursos, você pode facilmente renderizar templates HTML e organizar suas rotas de forma eficiente em aplicativos Gin. Isso ajuda a manter o código organizado e fácil de entender à medida que o projeto cresce.