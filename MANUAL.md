# MANUAL:
## INICIANDO O MOD:
- Antes de começar, certifique-se de ter o Go instalado em seu sistema. Você pode baixá-lo e instalá-lo em [golang.org](https://golang.org/dl/). Ou clique [aqui](https://github.com/VILHALVA/CURSO-DE-GOLANG) para fazer o curso de GOLANG.

Você pode inicializar um módulo Go no diretório do seu projeto. Você pode fazer isso executando o comando `go mod init` seguido do nome do seu módulo. Aqui está como você pode fazer:

1. No diretório do seu projeto, execute o seguinte comando para inicializar um módulo Go:

```bash
go mod init nome_do_seu_modulo
```

Substitua `nome_do_seu_modulo` pelo nome que você deseja dar ao seu módulo. Por exemplo:

```bash
go mod init meu_projeto
```

Isso criará um arquivo `go.mod` no diretório do seu projeto e o registrará como um módulo Go.

## INSTALAÇÃO DO GIN:
Você pode instalar o Gin usando o seguinte comando no terminal:

```bash
go get -u github.com/gin-gonic/gin
```

Isso instalará o pacote Gin e suas dependências em seu ambiente Go.

## CRIANDO O PROJETO:
Agora que você tem o Gin instalado, você pode criar um novo projeto:

```bash
mkdir meu_projeto && cd meu_projeto
```

Dentro do diretório do seu projeto, você pode criar um arquivo Go para começar a construir sua aplicação. Por exemplo, crie um arquivo `main.go` e adicione o seguinte código inicial:

```go
package main

import "github.com/gin-gonic/gin"

func main() {
    // Cria uma nova instância do Gin
    r := gin.Default()

    // Define uma rota simples
    r.GET("/", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "Olá, Mundo!",
        })
    })

    // Inicia o servidor na porta 8080
    r.Run(":8080")
}
```

Este é um exemplo simples de um aplicativo web usando Gin que responde com uma mensagem JSON "Olá, Mundo!" quando acessado na raiz (`/`) do servidor.

## EXECUTANDO O PROJETO:
Para executar seu projeto, você pode usar o comando `go run` no terminal:

```bash
go run main.go
```

Isso iniciará o servidor Gin na porta 8080 (ou na porta que você especificou no método `Run()`). Você pode acessar seu aplicativo em um navegador da web visitando [http://localhost:8080/](http://localhost:8080/).

Essas são as etapas básicas para instalar o Gin e criar um novo projeto em Go usando este framework. A partir daqui, você pode começar a expandir e desenvolver seu aplicativo da web conforme necessário, adicionando rotas, middlewares e funcionalidades adicionais.



