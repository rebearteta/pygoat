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
                docker { 
                    image 'ubuntu:22.04'
                    args '-u root'
                } 
            }
            steps {
                script {
                    sh 'apt-get update'
                    sh 'apt-get install wget apt-transport-https gnupg lsb-release -y'
                    sh 'wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null'
                    sh 'echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list'
                    sh 'apt-get install trivy -y'
                    sh 'trivy image --format json --output report-trivy-image.json rebecaarteta/pygoat:pygoat:1.0.0'
                    
                    archiveArtifacts artifacts: 'report-trivy-image.json', fingerprint: true, allowEmptyArchive: true
                }
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
