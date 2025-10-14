pipeline {
    agent none
    stages {
        stage('Safety') {
            agent {
                docker { image 'python:3.11' } 
            }
            steps {
                withCredentials([string(credentialsId: 'SAFETY_API_KEY', variable: 'API_KEY')]) {
                    sh '''
                        pip install safety
                        safety --key $API_KEY --stage cicd scan
                    '''
                }
            }
        }
        stage('Compilation') {
            agent {
                docker { image 'php:8.2-cli' }
            }
            steps {
                sh 'echo "Compilando..."'
            }
        }
        stage('Build') {
            agent {
                docker { image 'php:8.2-cli' }
            }
            steps {
                sh 'echo "docker build -t my-php-app ."'
            }
        }
        stage('Deploy') {
            agent {
                docker { image 'php:8.2-cli' }
            }
            steps {
                sh 'echo "docker run my-php-app ."'
            }
        }
    }
}
