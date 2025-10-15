pipeline {
    agent any

    tools {
            jdk 'JDK_21'
            maven 'Maven_3.9.11'
        }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Thiagzz/transparencia-salarial-esg.git'
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
                echo '🧪 Executando testes...'
                bat 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Construindo imagem Docker...'
                bat 'docker build -t transparencia-salarial-esg .'
            }
        }

        stage('Run Docker Container') {
            steps {
                echo '🚀 Subindo container local...'
                bat 'docker run -d -p 8080:8080 --name transparencia-api transparencia-salarial-esg'
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
