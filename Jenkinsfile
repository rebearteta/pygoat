pipeline {
    agent any
    stages {
        stage('Secret Scan - Gitleaks (docker agent)') { 
            agent { 
                docker { 
                    image 'zricethezav/gitleaks:latest' 
                    // args can usarse si necesitas montar volúmenes o ajustar opciones del contenedor 
                    // args '-v /host/path:/container/path' 
                    args '--entrypoint ""'
                    reuseNode true
                } 
            } 
            steps { 
                sh ''' 
                    set -eux 
                    # Ejecutar gitleaks dentro del contenedor (la imagen ya trae el binario) 
                    # Escanea el workspace actual; usa --no-git si no necesitas historial 
                    gitleaks detect \
                        --source . \
                        --no-git \
                        --report-format json \
                        --report-path gitleaks-report.json \
                        --redact \
                        --exit-code 1 
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
