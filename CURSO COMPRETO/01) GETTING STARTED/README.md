# 01) GETTING STARTED
Para começar a usar o Gin e criar seu primeiro projeto, siga as etapas abaixo:

1. **Inicialize um novo módulo Go:**

   No diretório onde você deseja criar seu projeto, inicialize um novo módulo Go. Abra seu terminal e execute:

   ```bash
   go mod init meu_projeto
   ```

   Isso criará um arquivo `go.mod` no diretório do seu projeto.

2. **Instale o Gin:**

   Como você já instalou o Gin com o comando `go get`, não há necessidade de repetir esta etapa. O Gin e suas dependências estão agora listados no seu arquivo `go.mod`.

3. **Crie um arquivo `main.go`:**

   Crie um novo arquivo chamado `main.go` no diretório do seu projeto e adicione o seguinte código:

   ```go
   package main

   import (
       "github.com/gin-gonic/gin"
   )

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

   Este código cria um servidor web simples usando Gin que responde com a mensagem "Olá, Mundo!" quando você acessa a raiz (`/`) do servidor.

4. **Execute o projeto:**

   No terminal, execute o comando para iniciar seu servidor:

   ```bash
   go run main.go
   ```

   Se tudo estiver configurado corretamente, você verá uma saída semelhante a esta:

   ```
   [GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

   [GIN-debug] GET    /                         --> main.main.func1 (3 handlers)
   [GIN-debug] Listening and serving HTTP on :8080
   ```

   Agora, você pode abrir um navegador web e acessar `http://localhost:8080/`. Você deverá ver a mensagem JSON "Olá, Mundo!".


# 02) CRIANDO ENTIDADE
## Estrutura Atualizada do Projeto
A estrutura do projeto permanece a mesma:

```
meu_projeto/
│
├── main.go
└── models/
    └── video.go
```

## Arquivo `video.go`
Certifique-se de que o arquivo `video.go` dentro do diretório `models` está definido assim:

```go
package models

type Video struct {
    ID       int    `json:"id"`
    Title    string `json:"title"`
    Director string `json:"director"`
}
```

## Arquivo `main.go` Atualizado
Agora, vamos atualizar o arquivo `main.go` para trabalhar com vídeos:

```go
package main

import (
    "net/http"
    "strconv"

    "github.com/gin-gonic/gin"
    "codigo/models"
)

var videos = []models.Video{
    {ID: 1, Title: "Inception", Director: "Christopher Nolan"},
    {ID: 2, Title: "The Matrix", Director: "The Wachowskis"},
}

func main() {
    r := gin.Default()

    // Rotas CRUD
    r.GET("/videos", getVideos)
    r.GET("/videos/:id", getVideoByID)
    r.POST("/videos", createVideo)
    r.PUT("/videos/:id", updateVideo)
    r.DELETE("/videos/:id", deleteVideo)

    // Inicia o servidor na porta 8080
    r.Run(":8080")
}

func getVideos(c *gin.Context) {
    c.JSON(http.StatusOK, videos)
}

func getVideoByID(c *gin.Context) {
    id, err := strconv.Atoi(c.Param("id"))
    if err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": "Invalid ID"})
        return
    }

    for _, video := range videos {
        if video.ID == id {
            c.JSON(http.StatusOK, video)
            return
        }
    }

    c.JSON(http.StatusNotFound, gin.H{"error": "Video not found"})
}

func createVideo(c *gin.Context) {
    var newVideo models.Video
    if err := c.ShouldBindJSON(&newVideo); err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
        return
    }

    newVideo.ID = len(videos) + 1
    videos = append(videos, newVideo)
    c.JSON(http.StatusCreated, newVideo)
}

func updateVideo(c *gin.Context) {
    id, err := strconv.Atoi(c.Param("id"))
    if err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": "Invalid ID"})
        return
    }

    var updatedVideo models.Video
    if err := c.ShouldBindJSON(&updatedVideo); err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
        return
    }

    for i, video := range videos {
        if video.ID == id {
            videos[i].Title = updatedVideo.Title
            videos[i].Director = updatedVideo.Director
            c.JSON(http.StatusOK, videos[i])
            return
        }
    }

    c.JSON(http.StatusNotFound, gin.H{"error": "Video not found"})
}

func deleteVideo(c *gin.Context) {
    id, err := strconv.Atoi(c.Param("id"))
    if err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": "Invalid ID"})
        return
    }

    for i, video := range videos {
        if video.ID == id {
            videos = append(videos[:i], videos[i+1:]...)
            c.JSON(http.StatusOK, gin.H{"message": "Video deleted"})
            return
        }
    }

    c.JSON(http.StatusNotFound, gin.H{"error": "Video not found"})
}
```

## Próximos Passos
1. **Executar o Projeto:** No terminal, dentro do diretório do projeto, execute:

    ```sh
    go run main.go
    ```

2. **Testar as Rotas:** Você pode usar Postman ou cURL para testar as rotas disponíveis:
    - `GET /videos`: Retorna todos os vídeos.
    - `GET /videos/:id`: Retorna um vídeo pelo ID.
    - `POST /videos`: Cria um novo vídeo. Envie um JSON no corpo da requisição, por exemplo:
        ```json
        {
            "title": "Interstellar",
            "director": "Christopher Nolan"
        }
        ```
    - `PUT /videos/:id`: Atualiza um vídeo existente pelo ID. Envie um JSON no corpo da requisição com os campos que deseja atualizar.
    - `DELETE /videos/:id`: Deleta um vídeo pelo ID.

Com esses passos, você agora tem uma aplicação básica usando Gin com operações CRUD para a entidade `Video`.

# 03) CRIANDO CONTROLLER
Vamos criar um controlador (controller) para gerenciar as operações relacionadas aos vídeos.

## Estrutura do Projeto
Primeiro, ajuste a estrutura do projeto para incluir o controlador:

```
\CODIGO\
│
├── main.go
├── controllers\
│   └── video_controller.go
└── models\
    └── video.go
```

## Código do Controller
Crie um arquivo `video_controller.go` na pasta `controllers`:

```go
package controllers

import (
    "net/http"
    "strconv"

    "github.com/gin-gonic/gin"
    "codigo/models"
)

var videos = []models.Video{
    {ID: 1, Title: "Inception", Director: "Christopher Nolan"},
    {ID: 2, Title: "The Matrix", Director: "The Wachowskis"},
}

// GetVideos retorna todos os vídeos
func GetVideos(c *gin.Context) {
    c.JSON(http.StatusOK, videos)
}

// GetVideoByID retorna um vídeo pelo ID
func GetVideoByID(c *gin.Context) {
    id, err := strconv.Atoi(c.Param("id"))
    if err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": "Invalid ID"})
        return
    }

    for _, video := range videos {
        if video.ID == id {
            c.JSON(http.StatusOK, video)
            return
        }
    }

    c.JSON(http.StatusNotFound, gin.H{"error": "Video not found"})
}

// CreateVideo cria um novo vídeo
func CreateVideo(c *gin.Context) {
    var newVideo models.Video
    if err := c.ShouldBindJSON(&newVideo); err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
        return
    }

    newVideo.ID = len(videos) + 1
    videos = append(videos, newVideo)
    c.JSON(http.StatusCreated, newVideo)
}

// UpdateVideo atualiza um vídeo existente
func UpdateVideo(c *gin.Context) {
    id, err := strconv.Atoi(c.Param("id"))
    if err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": "Invalid ID"})
        return
    }

    var updatedVideo models.Video
    if err := c.ShouldBindJSON(&updatedVideo); err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
        return
    }

    for i, video := range videos {
        if video.ID == id {
            videos[i].Title = updatedVideo.Title
            videos[i].Director = updatedVideo.Director
            c.JSON(http.StatusOK, videos[i])
            return
        }
    }

    c.JSON(http.StatusNotFound, gin.H{"error": "Video not found"})
}

// DeleteVideo deleta um vídeo
func DeleteVideo(c *gin.Context) {
    id, err := strconv.Atoi(c.Param("id"))
    if err != nil {
        c.JSON(http.StatusBadRequest, gin.H{"error": "Invalid ID"})
        return
    }

    for i, video := range videos {
        if video.ID == id {
            videos = append(videos[:i], videos[i+1:]...)
            c.JSON(http.StatusOK, gin.H{"message": "Video deleted"})
            return
        }
    }

    c.JSON(http.StatusNotFound, gin.H{"error": "Video not found"})
}
```

## Atualizar o `main.go`
Agora, atualize o `main.go` para usar o controlador que acabamos de criar. O `main.go` deve se parecer com isto:

```go
package main

import (
    "github.com/gin-gonic/gin"
    "codigo/controllers"
)

func main() {
    r := gin.Default()

    // Rotas CRUD
    r.GET("/videos", controllers.GetVideos)
    r.GET("/videos/:id", controllers.GetVideoByID)
    r.POST("/videos", controllers.CreateVideo)
    r.PUT("/videos/:id", controllers.UpdateVideo)
    r.DELETE("/videos/:id", controllers.DeleteVideo)

    // Inicia o servidor na porta 8080
    r.Run(":8080")
}
```

## Executar o Projeto
Certifique-se de que você está no diretório `CODIGO` e execute o projeto:

```sh
go run main.go
```

## Testando com Postman
Você pode usar o Postman para testar as seguintes rotas:

1. **Listar todos os vídeos (GET /videos)**
   - Método: `GET`
   - URL: `http://localhost:8080/videos`

2. **Obter um vídeo pelo ID (GET /videos/:id)**
   - Método: `GET`
   - URL: `http://localhost:8080/videos/1`

3. **Criar um novo vídeo (POST /videos)**
   - Método: `POST`
   - URL: `http://localhost:8080/videos`
   - Corpo (raw, JSON):
     ```json
     {
         "title": "Interstellar",
         "director": "Christopher Nolan"
     }
     ```

4. **Atualizar um vídeo existente (PUT /videos/:id)**
   - Método: `PUT`
   - URL: `http://localhost:8080/videos/1`
   - Corpo (raw, JSON):
     ```json
     {
         "title": "Updated Title",
         "director": "Updated Director"
     }
     ```

5. **Deletar um vídeo (DELETE /videos/:id)**
   - Método: `DELETE`
   - URL: `http://localhost:8080/videos/1`

Seguindo esses passos, você deve ser capaz de criar um controlador no seu projeto Gin e testá-lo usando o Postman.