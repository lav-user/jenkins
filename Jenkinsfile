pipeline {
    agent any
    environment {
        parabank_port=8080
    }
    stages {
        stage('build') {
            steps {
                sh '''
                echo ${pwd}
                # Clone parabank/main
                # Build with Jtest SA/UT/monitor
                '''
            }
        }
        
        stage('deploy') {
            steps {
                sh '''
                echo ${pwd}
                docker ps
                docker run -d \
                -p ${parabank_port}:8080 \
                -p 61616:61616 \
                -p 9001:9001 \
                parasoft/parabank
                '''
            }
        }
        stage('run tests') {
            steps {
                sh '''
                echo ${pwd}
                sleep 30
                docker ps
                curl -iv --raw http://localhost:8090/parabank
                '''
            }
        }
        stage('destroy') {
            steps {
                sh '''
                echo ${pwd}
                docker ps
                docker stop parasoft/parabank
                '''
            }
        }
    }
}
