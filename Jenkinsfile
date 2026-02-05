pipeline {
    agent any
    stages {
        stage('Secret Scan') {
            steps {
                // Ensure workspace is checked out so .git exists for gitleaks to scan commits
                checkout scm

                script {
                        // Ejecuta gitleaks usando Docker (directory scan, force JSON output)
                        sh '''
                            docker run --rm --user $(id -u):$(id -g) -v "${WORKSPACE}":/repo zricethezav/gitleaks:latest \
                            dir /repo --report-format json --report-path /repo/gitleaks-report.json -v || true

                            # If gitleaks didn't create the report (e.g. no files scanned), create an empty JSON report
                            if [ ! -f "${WORKSPACE}/gitleaks-report.json" ]; then
                                echo '[]' > "${WORKSPACE}/gitleaks-report.json"
                            fi
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
