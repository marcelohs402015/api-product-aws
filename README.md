# API Products AWS

[![Java](https://img.shields.io/badge/Java-21-ED8B00?logo=openjdk&logoColor=white)](https://openjdk.org/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2.1-6DB33F?logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![MongoDB](https://img.shields.io/badge/MongoDB-47A248?logo=mongodb&logoColor=white)](https://www.mongodb.com/)
[![AWS](https://img.shields.io/badge/AWS-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/)
[![Maven](https://img.shields.io/badge/Maven-C71A36?logo=apachemaven&logoColor=white)](https://maven.apache.org/)

API REST de produtos por categoria com persistência em MongoDB e publicação de eventos na AWS (SNS). Projeto de demonstração de habilidades em desenvolvimento de APIs, Spring Boot e deploy em ambiente cloud.
---
<div align="center">
  <h1>🚧 Em Construção 🚧</h1>
  <p>Este repositório está sendo atualizado constantemente. Novas funcionalidades em breve!</p>
  <img src="https://img.shields.io" alt="Status Badge">
</div>
---

## Sobre o projeto

API para gestão de **categorias** e **produtos**, com produtos vinculados a categorias. Operações de criação, atualização e exclusão de produtos disparam eventos no Amazon SNS para integração assíncrona (ex.: notificações, outros microsserviços).

---

## Funcionalidades

- **Categorias:** CRUD completo (`/api/category`)
- **Produtos:** CRUD completo (`/api/product`) com validação de categoria existente
- **Eventos:** Publicação no AWS SNS em insert/update/delete de produtos
- Persistência em **MongoDB**
- Pronto para deploy em **AWS** (EC2, ECS, Elastic Beanstalk, etc.)

---

## Stack

| Tecnologia        | Uso                    |
|-------------------|------------------------|
| Java 21           | Linguagem              |
| Spring Boot 3.2.1 | Framework              |
| Spring Data MongoDB | Persistência        |
| Spring Web        | REST API               |
| AWS SDK SNS       | Publicação de eventos  |
| Lombok            | Redução de boilerplate |
| Maven             | Build                  |

---

## Pré-requisitos

- **JDK 21**
- **Maven 3.8+**
- **MongoDB** (local ou Atlas)
- **Conta AWS** com SNS (topic configurado) para eventos

---

## Como rodar

### 1. Clone e entre no projeto

```bash
git clone https://github.com/<seu-usuario>/api-products-aws.git
cd api-products-aws
```

### 2. Configure o ambiente

Crie `src/main/resources/application.properties` (ou use variáveis de ambiente) com:

```properties
spring.data.mongodb.uri=mongodb://localhost:27017/api-products
spring.data.mongodb.database=api-products

aws.region=us-east-1
aws.sns.topic.catalog.arn=arn:aws:sns:us-east-1:ACCOUNT_ID:catalog-events
```

Para AWS (credenciais):

- Perfil em `~/.aws/credentials`, ou  
- Variáveis `AWS_ACCESS_KEY_ID` e `AWS_SECRET_ACCESS_KEY`, ou  
- IAM role (quando rodando na AWS)

### 3. Build e execução

```bash
./mvnw clean install
./mvnw spring-boot:run
```

A API sobe em `http://localhost:8080`.

---

## Variáveis de ambiente

| Variável                    | Descrição                          |
|----------------------------|------------------------------------|
| `SPRING_DATA_MONGODB_URI`  | URI de conexão MongoDB             |
| `AWS_REGION`               | Região AWS (ex.: us-east-1)        |
| `AWS_ACCESS_KEY_ID`        | Access key AWS (se não usar perfil)|
| `AWS_SECRET_ACCESS_KEY`    | Secret key AWS (se não usar perfil)|

O ARN do topic SNS pode ser configurado via `application.properties` ou variável equivalente, conforme o nome usado no `AwsSnsConfig`.

---

## Endpoints

### Categorias (`/api/category`)

| Método   | Endpoint       | Descrição           |
|----------|----------------|---------------------|
| `POST`   | `/api/category` | Cria categoria    |
| `GET`    | `/api/category` | Lista categorias  |
| `PUT`    | `/api/category/{id}` | Atualiza categoria |
| `DELETE` | `/api/category/{id}` | Remove categoria |

**Body (POST/PUT):** `{ "title": "string", "description": "string", "ownerId": "string" }`

### Produtos (`/api/product`)

| Método   | Endpoint       | Descrição           |
|----------|----------------|---------------------|
| `POST`   | `/api/product` | Cria produto (exige categoria existente) |
| `GET`    | `/api/product` | Lista produtos      |
| `PUT`    | `/api/product/{id}` | Atualiza produto |
| `DELETE` | `/api/product/{id}` | Remove produto (publica evento delete no SNS) |

**Body (POST/PUT):** `{ "title": "string", "description": "string", "ownerId": "string", "price": number, "categoryId": "string" }`

---

## Deploy na AWS

- **MongoDB:** use Atlas ou instância EC2 com MongoDB; informe a URI na config.
- **App:** gere o JAR com `./mvnw -DskipTests package` e execute com `java -jar target/api-product-aws-*.jar` em:
  - **EC2:** instância com JDK 21 e systemd/script de start.
  - **ECS/Fargate:** imagem baseada em `eclipse-temurin:21-jre`, apontando o task definition para o JAR ou para um build em camadas.
  - **Elastic Beanstalk:** plataforma Java 21, deploy do JAR ou via CodePipeline.
- **SNS:** crie um topic (ex.: `catalog-events`), configure o ARN na aplicação e políticas IAM para a instância/task publicar no SNS.

---

## Estrutura do projeto

```
src/main/java/com/mstech/apiproductaws/
├── config/
│   ├── aws/          # Configuração SNS
│   └── mongo/        # Configuração MongoDB
├── controllers/      # CategoryController, ProductController
├── domain/
│   ├── category/     # Category, CategoryDTO, exceções
│   └── product/      # Product, ProductDTO, exceções
├── repositories/     # Spring Data MongoDB
├── services/
│   ├── aws/          # AwsSnsService, MessageDTO
│   ├── CategoryService.java
│   └── ProductService.java
└── ApiproductawsApplication.java
```

---

## Licença

Este projeto é de código aberto para fins de estudo e portfólio.
