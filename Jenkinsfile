pipeline {
    agent any
    stages {
        stage('Secret Scan') {
            steps {
                script {
                    // Ejecuta gitleaks usando Docker
                    sh '''
                    docker run --rm -v ${PWD}:/repo zricethezav/gitleaks:latest \
                    detect --source /repo --verbose --report-path /repo/gitleaks-report.json
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
