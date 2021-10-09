FROM debian:stretch

ENV LANG C.UTF-8

# установка ansible и нужных для ее работы компонентов
RUN apt-get update
RUN apt-get install -y gnupg python-pip rsync
RUN pip install netaddr
RUN echo "deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main" > /etc/apt/sources.list.d/ansible.list
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
RUN apt-get update
RUN apt-get -y --allow-unauthenticated install ansible

# установка jdk-17
RUN apt-get install -y wget
RUN wget -O jdk17.tar.gz https://download.java.net/java/GA/jdk17/0d483333a00540d886896bac774ff48b/35/GPL/openjdk-17_linux-x64_bin.tar.gz
RUN mkdir -p /usr/java
RUN tar -xzvf jdk17.tar.gz -C /usr/java
RUN rm jdk17.tar.gz
RUN echo -e "JAVA_HOME=$(find /usr/java/jdk-17* -maxdepth 0)\nPATH=\$PATH:\$HOME/bin:\$JAVA_HOME/bin\nexport JAVA_HOME\nexport PATH" > /etc/profile.d/java.sh
RUN . /etc/profile.d/java.sh
RUN update-alternatives --install /usr/bin/java java $(find /usr/java/jdk-17* -maxdepth 0)/bin/java 50

# установка maven
# копировал отсюда https://github.com/carlossg/docker-maven/tree/master/openjdk-17
ARG MAVEN_VERSION=3.8.3
ARG USER_HOME_DIR="/root"
ARG SHA=1c12a5df43421795054874fd54bb8b37d242949133b5bf6052a063a13a93f13a20e6e9dae2b3d85b9c7034ec977bbc2b6e7f66832182b9c863711d78bfe60faa
ARG BASE_URL=https://apache.osuosl.org/maven/maven-3/${MAVEN_VERSION}/binaries

RUN apt-get install findutils git curl -y

RUN mkdir -p /usr/share/maven /usr/share/maven/ref \
  && curl -fsSL -o /tmp/apache-maven.tar.gz ${BASE_URL}/apache-maven-${MAVEN_VERSION}-bin.tar.gz \
  && echo "${SHA}  /tmp/apache-maven.tar.gz" | sha512sum -c - \
  && tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1 \
  && rm -f /tmp/apache-maven.tar.gz \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

ENV MAVEN_HOME /usr/share/maven
ENV MAVEN_CONFIG "$USER_HOME_DIR/.m2"

COPY mvn-entrypoint.sh /usr/local/bin/mvn-entrypoint.sh
RUN ["chmod", "+x", "/usr/local/bin/mvn-entrypoint.sh"]
COPY settings-docker.xml /usr/share/maven/ref/

ENTRYPOINT ["/usr/local/bin/mvn-entrypoint.sh"]
CMD ["mvn"]

