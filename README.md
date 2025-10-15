Transparência Salarial ESG

Este projeto faz parte da disciplina de DevOps e tem como objetivo implementar uma aplicação **Java Spring Boot + MongoDB** com **pipeline completo de CI/CD** utilizando **Jenkins** e **GitHub**, além de aplicar conceitos de **containerização e orquestração com Docker**.

A aplicação faz parte do contexto ESG (*Environmental, Social and Governance*), promovendo **transparência salarial** como parte das boas práticas sociais e de governança corporativa.

# Como executar localmente com Docker

- Passos para rodar a aplicação:

1. Adicione o repositório:
   https://github.com/Thiagzz/TransparenciaSalarialESG-CI-CD.git
   TransparenciaSalarialESG-CI-CD
Construa e suba os containers:

Acesse a aplicação:

Produção: http://localhost:8080

Staging: http://localhost:8081

MongoDB: mongodb://localhost:27017/transparencia

- Para parar os containers:

docker-compose down

# Pipeline CI/CD
Ferramentas utilizadas
Jenkins – Automação de build, testes e deploy

GitHub – Repositório remoto e integração com Jenkins

Maven – Build e gerenciamento de dependências

Docker – Containerização e deploy automatizado

Etapas do pipeline
Checkout

Obtém o código do repositório GitHub

Build

Compila o projeto Java com Maven

Teste

Executa os testes automatizados com JUnit 

Build Docker Image

Cria a imagem Docker da aplicação 

Deploy to Staging

Executa o container na porta 8081

Deploy to Production

Executa o container na porta 8080

Post

Exibe mensagens de sucesso ou falha ao final da execução

- Jenkinsfile

pipeline {
    agent any

    tools {
        jdk 'JDK_21'
        maven 'Maven_3.9.11'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Thiagzz/TransparenciaSalarialESG-CI-CD.git'
            }
        }

        stage('Build') {
            steps {
                echo ' Compilando projeto...'
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                echo ' Executando testes...'
                bat 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo ' Construindo imagem Docker...'
                bat 'docker build -t transparencia-salarial-esg .'
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo ' Deploy no ambiente STAGING...'
                bat 'docker run -d -p 8081:8080 --name transparencia-staging transparencia-salarial-esg'
            }
        }

        stage('Deploy to Production') {
            steps {
                echo ' Deploy no ambiente PRODUÇÃO...'
                bat 'docker run -d -p 8080:8080 --name transparencia-producao transparencia-salarial-esg'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline executado com sucesso!'
        }
        failure {
            echo '❌ Falha no pipeline!'
        }
    }
}
# Containerização
A aplicação é containerizada com um Dockerfile em JDK 21 com Docker Compose junto ao MongoDB.

- Dockerfile

FROM openjdk:21-jdk-slim
WORKDIR /app
COPY target/transparencia-salarial-esg-1.0.0.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
- docker-compose.yml

version: '3.8'

services:
  mongo:
    image: mongo:6.0
    container_name: mongo-db
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db
    networks:
      - transparencia-network

  transparencia-api:
    build: .
    container_name: transparencia-api
    ports:
      - "8080:8080"
    environment:
      - SPRING_DATA_MONGODB_URI=mongodb://mongo:27017/transparencia
      - SPRING_PROFILES_ACTIVE=prod
    depends_on:
      - mongo
    networks:
      - transparencia-network

volumes:
  mongo_data:

networks:
  transparencia-network:
  
Prints do funcionamento


Jenkins executando as etapas (build, test, deploy)
<img width="2559" height="540" alt="image" src="https://github.com/user-attachments/assets/8ad7759e-4ca7-432b-9509-c00d482b9e86" />
<img width="2540" height="1296" alt="image" src="https://github.com/user-attachments/assets/33af68cc-91cd-49b2-9a3e-14ef18f3baee" />
<img width="1287" height="324" alt="image" src="https://github.com/user-attachments/assets/090006e3-df22-4e0b-9a4d-d095fee1e432" />
<img width="1876" height="767" alt="image" src="https://github.com/user-attachments/assets/598e782b-cdc1-4269-b596-a4f70c67ade9" />

Containers rodando (via docker ps)
<img width="2559" height="750" alt="image" src="https://github.com/user-attachments/assets/86df1c21-f8e1-4509-9790-598627fb8bda" />

# Tecnologias utilizadas
Linguagem	Java 17
Framework	Spring Boot 3
Banco de Dados MongoDB
Build	Maven
CI/CD	Jenkins + GitHub
Containerização	Docker, Docker Compose
Testes	JUnit
