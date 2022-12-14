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
        ls_url="https://34.212.121.186:8443"
        ls_user='admin'
        ls_pass='parasoft.vm'
    }
    stages {
        stage('Validate') {
            steps {
                sh 'ls -R'
            }
        }
        stage('Build App') {
            when { equals expected: true, actual: false}
            steps {
                sh '''
                
                echo "Starting Pipeline Execution."
                echo "Create ./jtest directory for volume mount."
                mkdir jtest
                echo ${PWD}
                echo "Copy SOAtest project contents to volume directory."
                jtest/.
                # Check Vars
                echo $PWD
                
                # is Docker runnnig?
                docker ps
                ls -la

                # Build with Jtest SA/UT/monitor
                # Set Up and write .properties file

                echo  -e "\n~~~\nSetting up and creating jtest.properties file.\n"
                echo $"
                parasoft.eula.accepted=true
                jtest.license.use_network=true
                jtest.license.network.edition=server_edition
                dtp.url=${ls_url}
                dtp.user=${ls_user}
                dtp.password=${ls_pass}" >> jtest/jtestcli.properties
                echo -e "\nDebug -- Verify workspace contents.\n"
                ls -la
                echo -e "\nDebug -- Verify jtestcli.properties file contents."
                cat jtest/soatestcli.properties

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