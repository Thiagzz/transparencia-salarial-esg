pipeline {
    agent any

    tools {
        jdk 'JDK_21'
        maven 'Maven_3.9.11'
    }

    environment {
        IMAGE_NAME = 'transparencia-salarial-esg'
        STAGING_CONTAINER = 'transparencia-staging'
        PROD_CONTAINER = 'transparencia-prod'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Thiagzz/TransparenciaSalarialESG-CI-CD.git'
            }
        }

        stage('Build') {
            steps {
                echo '🔧 Compilando projeto...'
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
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo ' Deploy no ambiente STAGING...'
                bat '''
                docker stop %STAGING_CONTAINER% || true
                docker rm %STAGING_CONTAINER% || true
                docker run -d -p 8081:8080 --name %STAGING_CONTAINER% %IMAGE_NAME%
                '''
            }
        }

        stage('Deploy to Production') {
            when {
                branch 'master'
            }
            steps {
                echo ' Deploy no ambiente PRODUÇÃO...'
                bat '''
                docker stop %PROD_CONTAINER% || true
                docker rm %PROD_CONTAINER% || true
                docker run -d -p 8080:8080 --name %PROD_CONTAINER% %IMAGE_NAME%
                '''
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
