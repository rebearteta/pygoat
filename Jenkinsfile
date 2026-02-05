pipeline {
    agent any
    stages {
        stage('Secret Scan - Gitleaks (docker agent)') { 
            agent { 
                docker { 
                    image 'zricethezav/gitleaks:latest' 
                    args '--entrypoint ""'
                    reuseNode true
                } 
            } 
            steps { 
                sh ''' 
                    echo "Ejecutando análisis de secretos con Gitleaks..."
                    set -eux 
                    set +e
                    gitleaks detect \
                        --source . \
                        --report-format json \
                        --report-path gitleaks-report.json \
                        --redact \
                        --exit-code 1 
                    GITLEAKS_EXIT_CODE=$?
                    set -e

                    if [ $GITLEAKS_EXIT_CODE -eq 1 ]; then 
                        echo "Se encontraron secretos en el código." 
                        exit 1 
                    else 
                        echo "No se encontraron secretos." 
                    fi
                '''
            } 
            post { 
                always { 
                    archiveArtifacts artifacts: 'gitleaks-report.json', allowEmptyArchive: true 
                }
            }
        }
    }
}
