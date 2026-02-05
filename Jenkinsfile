pipeline {
    agent any
    stages {
        stage('Secret Scan - Gitleaks') {
          steps {
            sh '''
              set -eux
              docker run --rm \
                -v $WORKSPACE:/repo \
                zricethezav/gitleaks:latest detect \
                  --source /repo \
                  --no-git \
                  --report-format json \
                  --report-path /repo/gitleaks-report.json \
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
