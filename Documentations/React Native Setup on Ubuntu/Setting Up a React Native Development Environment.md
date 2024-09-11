# Setting Up a React Native Development Environment for Android APK Building on Ubuntu

## Introduction

This documentation guides you through establishing a development environment for creating React Native Android applications and building APKs on Ubuntu. It covers two approaches:

1. **Dedicated Ubuntu Instance**
2. **Docker**

**Prerequisites**

- Ubuntu operating system
- Basic understanding of command line usage
- An active internet connection

**Option 1: Dedicated Ubuntu Instance**

**Update and upgrade the so and packages**

```bash
apt update -y && apt upgrade -y
```

**Install required tools**

```bash
install -y build-essential curl wget unzip file gnupg
```

**Install node js 20**

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
    apt-get update && \
    apt-get install -y nodejs
```

**Check status of node and npm** 

```bash
node -v 
npm -v
```

**Install react native cli npm module**

```bash
npm install -g react-native-cli
# g installs globally
```

**Install OpenJDK version 21** 

```bash
apt install -y openjdk-21-jdk

java --version
```

**Add SDKmanager env to ~/.bashrc**

```bash
nano ~/.bashrc

Add entries
export ANDROID_SDK_ROOT=/opt/android-sdk
export PATH="$ANDROID_SDK_ROOT/cmdline-tools/cmdline-tools/bin:$PATH"

source ~/.bashrc
#install commandline tools

wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -O /tmp/tools.zip && \
    mkdir -p ${ANDROID_SDK_ROOT}/cmdline-tools && \
    unzip -q /tmp/tools.zip -d ${ANDROID_SDK_ROOT}/cmdline-tools && \
    rm /tmp/tools.zip
    
yes | sdkmanager --licenses

```

**Build the apk** 

```jsx
cd project_dir/android
chmod +x gradlew

./gradlew clean
./gradlew assembleRelease --stacktrace
```

**Output:**

**You will get the apk build on android/app/android/app/build/outputs/apk/release/app-release.apk**

**Option 2: Docker**

**Pull the ubuntu image 22.04**

```bash
FROM ubuntu:22.04
```

**Update & Upgrade OS and packages**

```bash
RUN apt update -y && apt upgrade -y
```

**Install required packages and node js** 

```bash
RUN apt-get install -y curl gnupg wget unzip  && \
    curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
    apt-get update && \
    apt-get install -y nodejs && \
    apt autoremove -y
RUN npm install -g react-native-cli
RUN apt install -y openjdk-21-jdk
```

**Check node js and java version** 

```bash
RUN java --version
node -v
npm -v
```

**Setting up Environments**

```bash
ENV JAVA_HOME /usr/lib/jvm/java-21-openjdk-amd64

ENV ANDROID_SDK_ROOT="/opt/android-sdk"
ENV PATH="$ANDROID_SDK_ROOT/cmdline-tools/cmdline-tools/bin:$PATH"
```

**Download and install sdkmanager**

```bash
RUN wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -O /tmp/tools.zip && \
    mkdir -p ${ANDROID_SDK_ROOT}/cmdline-tools && \
    unzip -q /tmp/tools.zip -d ${ANDROID_SDK_ROOT}/cmdline-tools && \
    rm /tmp/tools.zip
```

**Accept the sdkmanager polices** 

```bash
RUN yes | sdkmanager --licenses
```

**Setting up entrypoint** 

```bash
WORKDIR /app/android
COPY script.sh /
RUN chmod +x /script.sh
ENTRYPOINT ["/script.sh"]
```

**Entrypoint script** 

```bash
#!/bin/bash

# Make the Gradle wrapper executable
chmod +x gradlew

# Clean the project
./gradlew clean

# Assemble the release with stack trace
./gradlew assembleRelease --stacktrace

mkdir /output

cp -r /app/android/app/build/outputs/apk/release/* /output
```