pipeline {
    agent any
    stages {
        stage('build') {
            steps {
                sh '''
                echo ${pwd}
                docker ps
                '''
            }
        }
    }
}
