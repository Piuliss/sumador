pipeline {
    agent any

    options {
        timeout(time: 2, unit: 'MINUTES') // Tiempo máximo para la ejecución del pipeline
    }

    environment {
        CREDENTIALS_ID = 'f0142294-69d8-4e13-9215-33104e705eb6' 
        NEXUS_URL = "http://localhost:8083"
        IMAGE_NAME = "sumador_fiuni" // Nombre de la imagen Docker
        IMAGE_TAG = "${env.BUILD_NUMBER}" // Etiqueta de la imagen basada en el número de build
        HOSTDOCKERHUB = "honeyjack"
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

        stage('Tag Docker Image') {
            steps {
                echo "Tagging Docker image for Docker Hub"
                sh """
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${HOSTDOCKERHUB}/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
        stage('Vulnerability Scan - Docker ') {
            steps {
              sh "docker run  -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --severity=CRITICAL ${HOSTDOCKERHUB}/${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Deploy Image') {
          steps {
            withCredentials([usernamePassword(credentialsId: CREDENTIALS_ID, usernameVariable: 'NEXUS_USERNAME', passwordVariable: 'NEXUS_PASSWORD')]) {
                script {
                  docker.withRegistry("${NEXUS_URL}", "${CREDENTIALS_ID}") {
                    def imageName = "${IMAGE_NAME}:${IMAGE_TAG}"
                    def dockerImage = docker.build(imageName, '.')
                    dockerImage.push()
                  }
                }
            }
          }
        }
    }
}