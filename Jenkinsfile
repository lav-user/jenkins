pipeline {
    agent any
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    environment {
        parabank_git_url='https://github.com/parasoft/parabank.git'
        parabank_port=8090
    }
    stages {
        stage('build') {
            steps {
                cleanWs()
                sh 'echo "clone project"'
                git branch: 'master', url: 'https://github.com/parasoft/parabank.git'

                sh '''
                echo ${pwd}
                # Build with Jtest SA/UT/monitor
                # need jtestcli.properties
                # need maven in conatiner
                # configure maven

                docker run --rm --it \
                parasoft/jtest \
                ./mvnw clean install

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
                --name parabankv1 \
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
                docker stop parabankv1
                docker rm parabankv1
                '''
            }
        }
    }
}
