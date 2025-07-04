pipeline {
    agent any

    environment {
        imageREF = "aadarkdk/myapp"
    }

    stages {
        stage('Build') {
            steps {
                echo 'validate --> compile --> test --> package the java project.'
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving it...'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Build Docker image') {
            steps {
                echo 'Building the docker image...'
                sh 'whoami'
                sh 'docker image build -t ${imageREF}:${BUILD_NUMBER} .'
            }
        }

        stage('Scan image using trivy') {
            steps {
                echo 'Scanning the image...'
                sh 'trivy image ${imageREF}:${BUILD_NUMBER}'
            }
        }

        stage('Push image to DockerHub') {
            steps {
                echo 'Pushing the image to DockerHub...'
                withDockerRegistry([credentialsId: 'dockerhub-credentials', url: '']) {
                    sh 'docker push ${imageREF}:${BUILD_NUMBER}'
                }
            }
        }

        stage('Deploy the web app') {
            steps {
                echo 'Deploying the maven CICD on http://192.168.56.9:8082/'
                sh """
                docker container stop myapp || true
                docker container rm myapp || true
                docker image rm ${imageREF}:${BUILD_NUMBER} || true
                docker run -d --name myapp -p 8082:8080 ${imageREF}:${BUILD_NUMBER}
                """
                sh 'docker ps | grep myapp'

            }
        }
    }
}
