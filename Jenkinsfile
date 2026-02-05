pipeline {
    agent any
    stages {
        stage('Secret Scan') {
            steps {
                script {
                    // Ejecuta gitleaks usando Docker
                    sh '''
                        docker run --rm -v "${WORKSPACE}":/repo zricethezav/gitleaks:latest \
                          dir /repo -v --report-path /repo/gitleaks-report.json || true
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
