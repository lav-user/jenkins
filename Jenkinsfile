pipeline {
    agent any
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    environment {
        // app_git_url='https://github.com/parasoft/parabank.git'
        // app_branch='master'
        parabank_port=8090
    }
    stages {
        stage('Build App') {
            steps {
                cleanWs()
                sh 'echo "clone project"'
                git branch: 'master', url: 'https://github.com/parasoft/parabank.git'

                sh '''
                # Check Vars
                echo $PWD
                
                # is Docker runnnig?
                docker ps
                ls -la

                # Build with Jtest SA/UT/monitor
                # need jtestcli.properties
                # need to point to ${user.home}
                
                docker run --rm -i \
                -u 0:0 \
                -v "$PWD:$PWD" \
                -w "$PWD" \
                jtest:mvn /bin/bash -c " \
                cat $MAVEN_CONFIG/settings.xml; \
                mvn jtest:jtest -X \
                -s /home/parasoft/.m2/settings.xml \
                -Djtest.settings='/home/parasoft/jtestcli.properties'; \
                mvn package jtest:monitor \
                -s /home/parasoft/.m2/settings.xml \
                -Djtest.settings='/home/parasoft/jtestcli.properties'; \
                "
                '''
            }
        }
        
        stage('Deploy App via Docker') {
            when { equals expected: true, actual: false}
            steps {
                sh '''
                echo ${pwd}
                docker ps

                # Set Up Env
                # Unpack monitor
                # JDBCDriver.jar

                # Deploy App
                ## Specify Port via ${parabank_port}
                docker run -d \
                -p ${parabank_port}:8080 \
                -p 61616:61616 \
                -p 9001:9001 \
                --name parabankv1 \
                parasoft/parabank

                # Configure App

                # Deploy Virt Env
                '''
            }
        }
        stage('Run Functional Tests') {
            when { equals expected: true, actual: false}
            steps {
                sh '''
                echo ${pwd}
                
                # Wait for start up
                sleep 30
                docker ps

                # Pulse?
                curl -iv --raw http://localhost:8090/parabank
                
                # License SOATest
                
                # Run SOAtest Tests
                '''


            }
        }
        stage('Destroy Contatiners and Clean Up') {
            when { equals expected: true, actual: false}
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
    post {
        always {
            archiveArtifacts artifacts: '**/target/jtest/**', 
            fingerprint: true, 
            onlyIfSuccessful: true
        }
    }
}