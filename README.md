# Parabank build/deploy/test/promote
The followig repo conatins automation script for Jenkins to run a full build of Parabank with Jtest/SOAtest/Virtualize/otehr tool vi ephemaral Docker conatiners

## Project Contents

```
jenkins
 ┣ jtest
 ┃ ┣ .m2
 ┃ ┃ ┗ settings.xml
 ┃ ┣ Dockerfile
 ┃ ┗ jtestcli.properties
 ┣ .gitignore
 ┣ Jenkinsfile
 ┗ README.md
```

## Jtest

### Configure Jtest

To Jtest to run configure `jtest/jtestcli.properties` file.

The Dockerfile will create a Jtest docker container with Maven installed. The settings.xml file and `jtestcli.properties` file is copied into the `/home/parasoft/` directory.

to run jtestcli use
```
mvn jtest:jtest \
-s /home/parasoft/.m2/settings.xml \
-Djtest.settings='/home/parasoft/jtestcli.properties'; \
```

TODO:
fix docker file so that the -s and -Djtest.settings flags arent needed.