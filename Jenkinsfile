pipeline {
    agent none
    parameters {
        booleanParam(name: 'BLOCK_ON_FINDINGS', defaultValue: true, description: 'Bloquear el pipeline si hay HIGH/CRITICAL')
        choice(name: 'SCAN_TARGET', choices: ['image','filesystem','both'], description: 'Qué escanear con Trivy')
        string(name: 'IMAGE_NAME', defaultValue: 'myjenkins-blueocean', description: 'Nombre de la imagen a escanear')
        string(name: 'IMAGE_TAG',  defaultValue: '2.516.3-1', description: 'Tag de la imagen a escanear')
        string(name: 'SEVERITIES',  defaultValue: 'CRITICAL,HIGH', description: 'Severidades a considerar')
        booleanParam(name: 'IGNORE_UNFIXED', defaultValue: true, description: 'Ignorar vulnerabilidades sin fix disponible')
    }
    environment {
        TRIVY_CACHE_DIR = "${WORKSPACE}/.trivycache"
        REPORT_DIR      = "${WORKSPACE}/reports"
        // Si BLOCK_ON_FINDINGS=true => exit-code=1 (falla el build). Si no, exit-code=0.
        TRIVY_EXIT_CODE = "${params.BLOCK_ON_FINDINGS ? '1' : '0'}"
        TRIVY_IGNORE    = "${params.IGNORE_UNFIXED ? '--ignore-unfixed' : ''}"
    }
    stages {
        stage('Trivy Scan') {
            agent {
                docker { 
                    image 'python:3.11'
                    args '-u root'
                } 
            }
            steps {
                script {
                    try {
                        sh """
                        mkdir -p "${REPORT_DIR}" "${TRIVY_CACHE_DIR}"
                        docker run --rm \
                            -v /var/run/docker.sock:/var/run/docker.sock \
                            -v "${TRIVY_CACHE_DIR}":/root/.cache/ \
                            -v "${WORKSPACE}":/workspace \
                            aquasec/trivy:latest \
                            --cache-dir /root/.cache/ \
                            image ${TRIVY_IGNORE} \
                            --scanners vuln,secret,config \
                            --severity "${SEVERITIES}" \
                            --format json  -o /workspace/reports/trivy-image.json \
                            --format sarif -o /workspace/reports/trivy-image.sarif \
                            --exit-code 1 \
                            "${IMAGE_REF}"
                        """
                        echo "Trivy no encontró findings bloqueantes."
                    } catch (err) {
                        if (!params.BLOCK_ON_FINDINGS) {
                            unstable("Trivy detectó vulnerabilidades: marcando UNSTABLE (no falla).")
                        } else {
                            error("Trivy detectó vulnerabilidades: FAIL por política.")
                        }
                    } finally {
                        archiveArtifacts artifacts: 'reports/.json, reports/.sarif', fingerprint: true
                    }
                }
            }
        }
        stage('Safety') {
            agent {
                docker { 
                    image 'python:3.11'
                    args '-u root'
                } 
            }
            steps {
                withCredentials([string(credentialsId: 'SAFETY_API_KEY', variable: 'API_KEY')]) {
                    sh '''
                        pip install safety
                        set +e
                        safety --key $API_KEY --stage cicd scan  --output json > safety-report.json
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
    post {
        always {
            archiveArtifacts artifacts: 'safety-report.json', fingerprint: true
        }
    }
}
