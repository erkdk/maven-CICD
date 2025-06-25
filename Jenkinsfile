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
        stage('Test') {
            steps {
                echo 'Test script here......'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy script here.....'
            }
        }
    }
}
