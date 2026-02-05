pipeline {
    agent any
    stages {
        stage('Secret Scan - Gitleaks') {
          steps {
            sh '''
              set -eux
              docker run --rm \
                -v $WORKSPACE:$WORKSPACE \
                -w $WORKSPACE \
                zricethezav/gitleaks:latest detect \
                  --source $WORKSPACE \
                  --report-format json \
                  --report-path $WORKSPACE/gitleaks-report.json \
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
