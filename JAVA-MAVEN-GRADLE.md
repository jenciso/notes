## Installing java 8 - Ubuntu 18.04

```shell
export $JAVA_VERSION=8
apt-get update && \
        apt-get install --no-install-recommends \
        apt-transport-https \
        gnupg-agent \
        software-properties-common \
        openjdk-${JAVA_VERSION}-jdk
```

```shell
cat << EOF >> ~/.bashrc
### JAVA
export JAVA_VERSION=8
export JAVA_HOME=/usr/lib/jvm/java-${JAVA_VERSION}-openjdk-amd64
export JAVA_HOME_${JAVA_VERSION}_X64=/usr/lib/jvm/java-${JAVA_VERSION}-openjdk-amd64
EOF

source ~/.bashrc

java -version
```

## Installing Maven

```shell
export MAVEN_VERSION=3.6.3
export BASE_URL=https://apache.osuosl.org/maven/maven-3/${MAVEN_VERSION}/binaries
sudo mkdir -p /usr/share/maven /usr/share/maven/ref
curl -fsSL -o /tmp/apache-maven.tar.gz ${BASE_URL}/apache-maven-${MAVEN_VERSION}-bin.tar.gz
sudo tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1
rm -f /tmp/apache-maven.tar.gz
sudo ln -s /usr/share/maven/bin/mvn /usr/bin/mvn
```

```shell
cat << EOF >> ~/.bashrc
## MAVEN
export MAVEN_HOME=/usr/share/maven
export MAVEN_CONFIG=~/.m2
EOF

source ~/.bashrc
mvn --version
```

## Installing Gradle

```shell
export GRADLE_VERSION=6.3
export GRADLE_BASE_URL=https://services.gradle.org/distributions
sudo mkdir -p /usr/share/gradle /usr/share/gradle/ref
curl -fsSL -o /tmp/gradle.zip ${GRADLE_BASE_URL}/gradle-${GRADLE_VERSION}-bin.zip
sudo unzip -d /usr/share/gradle /tmp/gradle.zip
rm -f /tmp/gradle.zip
sudo ln -s /usr/share/gradle/gradle-${GRADLE_VERSION}/bin/gradle /usr/bin/gradle
```

```shell
cat << EOF >> ~/.bashrc
## GRADLE
export GRADLE_VERSION=6.3
export GRADLE_HOME=/usr/share/gradle/gradle-${GRADLE_VERSION}
EOF

source ~/.bashrc
gradle --version
```
