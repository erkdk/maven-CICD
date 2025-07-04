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
                echo 'Deploying the Maven CI/CD app on http://192.168.56.9:8082/'
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

    post {
        always {
            echo 'Cleaning up workspace and notifying default contact...'
            mail to: 'milankhadka204@gmail.com',
                subject: "Job '${JOB_NAME}' (#${BUILD_NUMBER}) finished",
                body: "Build result: ${currentBuild.currentResult}\nVisit: ${BUILD_URL}"
            cleanWs()
        }

        success {
            mail to: 'meaadar1@gmail.com',
                subject: 'BUILD SUCCESS NOTIFICATION',
                body: """Hi Team,

Build #${BUILD_NUMBER} was successful.

View it here: ${BUILD_URL}

Regards,
DevOps Team"""
            }
        
        failure {
            mail to: 'aayush.kdk77@gmail.com',
                subject: 'BUILD FAILED NOTIFICATION',
                body: """Hi Team,

Build #${BUILD_NUMBER} failed.

Check the details here: ${BUILD_URL}

Regards,
DevOps Team"""
            }
    }
}
