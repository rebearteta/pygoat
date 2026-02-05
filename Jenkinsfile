pipeline {
    agent any
    stages {
        stage('Secret Scan') {
            steps {
                script {
                    // Ejecuta gitleaks usando Docker
                    sh '''
                        docker run --rm \
                          -e GIT_CONFIG_COUNT=1 \
                          -e GIT_CONFIG_KEY_0=safe.directory \
                          -e GIT_CONFIG_VALUE_0=/repo \
                          -v "${WORKSPACE}":/repo zricethezav/gitleaks:latest \
                          detect --source /repo --verbose --report-path /repo/gitleaks-report.json || true
                    '''
                }
            }
            post {
                always {
                    // Archiva el reporte para revisarlo después
                    archiveArtifacts artifacts: 'gitleaks-report.json', fingerprint: true, allowEmptyArchive: true
                }
            }
        }
    }
}
