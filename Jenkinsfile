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
                sh 'echo "building..."'
            }
        }
        stage('TruffleHog Scan') {
            agent {
                docker { 
                    image 'ubuntu:22.04'
                    args '-u root'
                } 
            }
            steps {
                script {
                    sh 'docker run --rm -it -v "$PWD:/pwd" trufflesecurity/trufflehog:latest github --repo https://github.com/trufflesecurity/test_keys'
                    sh 'trufflehog git https://github.com/rebearteta/pygoat --results=verified --json > trufflehog-report.json'
                    
                    archiveArtifacts artifacts: 'trufflehog-report.json', fingerprint: true, allowEmptyArchive: true
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
