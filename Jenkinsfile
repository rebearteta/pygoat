pipeline {
    agent none
    stages {
        stage('Safety') {
            agent {
                docker { image 'python:3.11' } 
            }
            steps {
                sh '''
                    pip install safety
        
                      # Informe legible en consola (no detiene el job por sí solo)
                      safety check --file=requirements.txt --full-report || true
            
                      # Genera JSON y captura exit code para decidir el resultado
                      set +e
                      safety check --file=requirements.txt --output json > safety-report.json
                      STATUS=$?
                      set -e
            
                      # Falla el build si hubo vulnerabilidades
                      if [ "$STATUS" -ne 0 ]; then
                        echo "Safety encontró vulnerabilidades. Revisa safety-report.json"
                        exit 1
                      fi
                '''
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
