pipeline {
    agent {
        label 'docker'
    }

    environment {
        TARGET_HOST = 'www.webgoat.local'
        WEBGOAT_URL = "http://${TARGET_HOST}:8080/WebGoat"
        ZAP_IMAGE = 'ghcr.io/zaproxy/zaproxy:stable'
        TZ = 'America/Boise'
    }
  
        stage('Set Up /etc/hosts') {
            steps {
                sh '''
                    if ! grep -q "${TARGET_HOST}" /etc/hosts; then
                        echo "127.0.0.1 ${TARGET_HOST} www.webwolf.local" | sudo tee -a /etc/hosts
                    fi
                '''
            }
        }

        stage('Run WebGoat in Docker') {
            steps {
                sh '''
                    docker pull webgoat/webgoat
                    docker run -d --rm \
                        --name webgoat \
                        -e TZ=${TZ} \
                        -e WEBGOAT_HOST=${TARGET_HOST} \
                        -e WEBWOLF_HOST=www.webwolf.local \
                        -p 8080:8080 -p 9090:9090 \
                        webgoat/webgoat
                    sleep 20
                '''
            }
        }

        stage('Run ZAP Full Scan') {
            steps {
                sh '''
                    docker run --network=host \
                        -v $(pwd):/zap/wrk/ ${ZAP_IMAGE} zap-full-scan.py \
                        -t ${WEBGOAT_URL} \
                        -r zap-webgoat-report.html \
                        -J zap-webgoat-report.json \
                        -a
                '''
            }
        }

        stage('Archive ZAP Report') {
            steps {
                archiveArtifacts artifacts: 'zap-webgoat-report.*', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            echo 'Cleaning up Docker containers...'
            sh 'docker stop webgoat || true'
        }
    }
}
