pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'validate -->  compile -->  test -->  package'
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving it...'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Docker image') {
            steps {
                echo 'Building the docker image......'
                sh 'whoami'
                sh 'docker image build -t mylocalrepo/simplejavaap: $BUILD_NUMBER'
            }
        }
        stage('Scan image') {
            steps {
                echo 'Scanning docker image......'
            }
        }
        stage('Push image') {
            steps {
                echo 'Scanning docker image script here......'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy script here.....'
            }
        }
    }
}
