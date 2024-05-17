# GETTING STARTED WITH GORM - GOLANGS ORM LIBRARY
Para começar a usar o GORM, a biblioteca ORM (Object-Relational Mapping) para Go, siga estes passos:

## 1. Instalação
Certifique-se de ter o GORM instalado. Você pode instalá-lo usando o seguinte comando:

```bash
go get -u gorm.io/gorm
```

## 2. Configuração do Banco de Dados
Antes de começar a usar o GORM, você precisa configurar sua conexão com o banco de dados. O GORM suporta vários bancos de dados, como MySQL, PostgreSQL, SQLite e SQL Server.

Aqui está um exemplo de como você pode configurar uma conexão com o banco de dados MySQL:

```go
package main

import (
    "gorm.io/driver/mysql"
    "gorm.io/gorm"
)

func main() {
    dsn := "user:password@tcp(localhost:3306)/dbname?charset=utf8mb4&parseTime=True&loc=Local" // Substitua user, password, dbname de acordo com suas configurações
    db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
    if err != nil {
        panic("Failed to connect to database")
    }
    
    // Use 'db' para realizar operações de banco de dados
    // db.AutoMigrate(&YourModel{}) para migrar modelos
}
```

## 3. Definindo Modelos
Defina os modelos de dados que correspondem às tabelas do banco de dados. Aqui está um exemplo simples de um modelo de usuário:

```go
package models

import "gorm.io/gorm"

type User struct {
    gorm.Model
    Name  string
    Email string
}
```

## 4. Realizando Operações CRUD
Com o GORM configurado e modelos definidos, você pode realizar operações CRUD no banco de dados. Aqui estão alguns exemplos:

```go
// Criar um novo usuário
func createUser(db *gorm.DB, user *models.User) error {
    return db.Create(user).Error
}

// Obter um usuário por ID
func getUserByID(db *gorm.DB, id uint) (*models.User, error) {
    var user models.User
    if err := db.First(&user, id).Error; err != nil {
        return nil, err
    }
    return &user, nil
}

// Atualizar um usuário
func updateUser(db *gorm.DB, user *models.User, newData *models.User) error {
    return db.Model(&user).Updates(newData).Error
}

// Deletar um usuário
func deleteUser(db *gorm.DB, user *models.User) error {
    return db.Delete(&user).Error
}
```

## 5. Executar Migrações
Se você estiver usando o GORM para criar suas tabelas automaticamente com base nos modelos, pode usar o método `AutoMigrate` para isso:

```go
db.AutoMigrate(&User{})
```

## 6. Testando
Agora que você configurou o GORM e definiu seus modelos, pode testar suas operações CRUD.

Com estes passos, você estará pronto para começar a trabalhar com o GORM em seus projetos Go, facilitando a interação com o banco de dados. Certifique-se de consultar a documentação oficial do GORM para obter mais detalhes e recursos avançados.