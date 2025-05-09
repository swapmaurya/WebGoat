pipeline {
    agent any

    environment {
        TARGET_HOST = 'www.webgoat.local'
        WEBGOAT_URL = "http://${TARGET_HOST}:8080/WebGoat"
    }

    stages {
        stage('Check Docker status') {
            steps {
                sh '''
                    uname -a
                    ps -p 1 -o comm=
                    sudo dockerd &
                '''
            }
        }

        stage('Install WebGoat on Docker') {
            steps {
                sh '''
                    echo "lets start installation"
                    docker run -it -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 webgoat/webgoat
                '''
            }
        }

    }

    post {
        always {
            echo 'Cleaning up Docker containers...'
        }
    }
}
