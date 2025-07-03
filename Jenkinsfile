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

        stage('Deploy') {
            steps {
                echo 'Deploy script here...'
                // You can run a shell script or kubectl/docker-compose command here
            }
        }
    }
}
