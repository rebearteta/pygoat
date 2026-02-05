pipeline {
  agent {
    docker {
      image 'python:3.12-slim'
    }
  }
  environment {
    HOME = "${WORKSPACE}" // evita que pip use /.local
    PIP_CACHE_DIR = "${WORKSPACE}/.pip-cache"
    PIP_DISABLE_PIP_VERSION_CHECK = "1"
    PROJECT_NAME = 'pygoat'
    PROJECT_VER = "build-${BUILD_NUMBER}"
    SBOM_FILE = 'bom.xml'
  }
  stages {
    /*stage('Security gate - Bandit (HIGH)') {
      steps {
        sh '''
          set -eux
          python -m pip install --user bandit

          # Gate: solo severidad HIGH (y opcionalmente confianza HIGH)
          python -m bandit -r . --severity-level high --confidence-level high -f json -o bandit.json
        '''
      }
      post {
        always {
          archiveArtifacts artifacts: 'bandit.json', fingerprint: true
        }
      }
    }

    stage('Generate SBOM') {
      steps {
        sh '''
          set -eux
          python -m pip install --user cyclonedx-bom

          if [ -f requirements.txt ]; then
            cyclonedx-py requirements requirements.txt --of XML --sv 1.6 -o "${SBOM_FILE}"
          else
            cyclonedx-py environment --of XML --sv 1.6 -o "${SBOM_FILE}"
          fi

          ls -lah "${SBOM_FILE}"
        '''
      }
    }

    stage('Upload to Dependency-Track') {
      steps {
        dependencyTrackPublisher(
          projectName: "${env.PROJECT_NAME}",
          projectVersion: "${env.PROJECT_VER}",
          artifact: "${env.SBOM_FILE}",
          synchronous: true
        )
      }
    }*/

    stage('Secret Scan - Gitleaks') {
      steps {
        sh '''
          set -eux
          # Descargar binario de Gitleaks
          curl -sSL https://github.com/gitleaks/gitleaks/releases/download/v8.18.0/gitleaks_8.18.0_linux_x64.tar.gz | tar -xz

          ./gitleaks version

          # Escanea el repo del workspace. Si hay leaks, devuelve exit code 1 y falla el stage.
          ./gitleaks detect \
            --source . \
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

    stage('Build') {
      steps {
        sh 'echo "Nombre del stage Build..."'
      }
    }

    stage('Test') {
      steps {
        sh 'echo "Nombre del stage Test..."'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: "${SBOM_FILE}", fingerprint: true
    }
  }
}
