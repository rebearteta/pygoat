pipeline {
    agent none
    stages {
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
                docker { 
                    image 'docker:28.5.1-cli' 
                    args '-u root'
                }
            }
            steps {
                sh 'echo "docker build -t pygoat:1.0.0 ."'
                sh 'echo "docker push rebecaarteta/pygoat:pygoat:1.0.0"'
            }
        }
        stage('Trivy Scan') {
            agent {
                docker { image 'aquasecurity/trivy:latest' }
            }
            steps {
                sh 'trivy image --format json --output report-trivy-image.json rebecaarteta/pygoat:pygoat:1.0.0'
                archiveArtifacts artifacts: 'report-trivy-image.json', fingerprint: true, allowEmptyArchive: true
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
