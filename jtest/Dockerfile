# baseline image
FROM parasoft/jtest

# Copy Deps

# Install MVN

COPY jtestcli.properties /home/parasoft/jtestcli.properties
COPY .m2 ./.m2

# Permissions to create dirs
USER root
RUN chown -R parasoft:parasoft .

# MVN args
ARG MAVEN_VERSION=3.6.3
ARG USER_HOME_DIR=/home/parasoft/
ARG BASE_URL=https://apache.osuosl.org/maven/maven-3/${MAVEN_VERSION}/binaries
RUN mkdir -p /usr/share/maven /usr/share/maven/ref \
 && curl -fsSL -o /tmp/apache-maven.tar.gz ${BASE_URL}/apache-maven-${MAVEN_VERSION}-bin.tar.gz \
 && tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1 \
 && rm -f /tmp/apache-maven.tar.gz \
 && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

# Switch user back and configure MVN
USER parasoft:parasoft
ENV MAVEN_HOME /usr/share/maven
ENV MAVEN_CONFIG "$USER_HOME_DIR/.m2"

# Define working directory.
WORKDIR $USER_HOME_DIR

# Define commonly used JAVA_HOME variables

# Define default command.
CMD ["mvn", "--version"]