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
        ls_url="${PARASOFT_LS_URL}"
        ls_user="${PARASOFT_LS_USER}"
        ls_pass="${PARASOFT_LS_PASS}"
    }
    stages {
        stage('Configre Workspace') {
            steps {
                cleanWs()
                sh '''
                    echo ${ls_user}
                    ls -R    
                    
                    git clone 'https://github.com/gtrofimov/jenkins.git' 
                
                    git clone 'https://github.com/parasoft/parabank.git'
                    ls -la
                
                   '''
            }
        }
        stage('Build App') {
            when { equals expected: true, actual: true}
            steps {
                sh '''
                
                echo "Starting Pipeline Execution."
                echo ${PWD}

                # Check Vars
                echo $PWD
                mkdir monitor
                set MONITOR_HOME="monitor"
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
                dtp.password=${ls_pass}" >> jenkins/jtest/jtestcli.properties
                echo -e "\nDebug -- Verify workspace contents.\n"
                ls -la
                echo -e "\nDebug -- Verify jtestcli.properties file contents."
                cat jenkins/jtest/jtestcli.properties

                # need to point to ${user.home}
                
                docker run --rm -i \
                -u 0:0 \
                -v "$PWD:$PWD" \
                -w "$PWD" \
                $(docker build -q ./jenkins/jtest) /bin/bash -c " \
                cd parabank; \
                mvn \
                -DskipTests=true \
                package jtest:monitor \
                -s /home/parasoft/.m2/settings.xml \
                -Djtest.settings='/home/parasoft/jtestcli.properties'; \
                "

                # Unzip monitor.zip
                unzip **/target/*/*/monitor.zip -d monitor
                ls -la monitor
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
                -p 8050:8050 \
                -p 61616:61616 \
                -p 9001:9001 \
                -e CATALINA_OPTS=-javaagent:"/home/docker/jtest/monitor/agent.jar"=settings="/home/docker/jtest/monitor/agent.properties",runtimeData="/home/docker/jtest/monitor/runtime_coverage" \
                -v "$PWD/monitor:/home/docker/jtest/monitor" \
                --name parabankv1 \
                parasoft/parabank

                # Configure App

                # Deploy Virt Env
                '''
            }
        }
        stage('Run Functional Tests') {
            when { equals expected: true, actual: true}
            steps {
                sh '''
                echo ${pwd}
                
                # Wait for start up
                sleep 30
                docker ps

                # Pulse?
                curl -iv --raw http://localhost:8090/parabank
                curl -iv --raw http://localhost:8050/status
                # License SOATest
                
                # Run SOAtest Tests
                '''


            }
        }
        stage('Destroy Contatiners and Clean Up') {
            when { equals expected: true, actual: true}
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