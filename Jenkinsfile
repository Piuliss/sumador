pipeline {
    agent any

    options {
        timeout(time: 2, unit: 'MINUTES') // Tiempo máximo para la ejecución del pipeline
    }

    environment {
        IMAGE_NAME = "sumador" // Nombre de la imagen Docker
        IMAGE_TAG = "${env.BUILD_NUMBER}" // Etiqueta de la imagen basada en el número de build
    }

    stages {
        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh """
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Test Docker Image') {
            steps {
            sh "docker run ${IMAGE_NAME}:${IMAGE_TAG} npm test"
          }
        }
    }
}