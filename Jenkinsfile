pipeline {
    agent any
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
        stage('Vault') {
            steps {
                script {
                    def secrets = [
                        [path: 'jenkins/creds/test', engineVersion: 1, secretValues: [
                            [envVar: 'testing', vaultKey: 'secret']]
                        ],
                    ]
                    withVault([vaultSecrets: secrets]) {
                        sh 'echo $testing | base64 > secret.txt'
                        sh 'cat secret.txt'
                    }
                    archiveArtifacts artifacts: 'secret.txt', fingerprint: true, allowEmptyArchive: true
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
