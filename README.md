## Android Docker Image for Building and Deploying Apps to Google Play Store

![main](https://github.com/mohamed-helmy/android-docker/actions/workflows/docker-image.yml/badge.svg)

This Docker image is designed to streamline the process of building and deploying Android applications to the Google Play Store. It includes the Android SDK and Fastlane, enabling developers to easily integrate the process into their CI/CD pipelines.

### Features:

- **Android SDK**: The latest Android SDK is pre-installed, allowing you to build and package your Android apps.
- **Fastlane**: Fastlane is installed, simplifying the process of building and deploying Android apps. It automates tedious tasks, such as generating screenshots, code signing, and releasing apps.
### Supported Tags
- `mohamedhelmy/android-docker:24`
- `mohamedhelmy/android-docker:33`
- `mohamedhelmy/android-docker:32`
- `mohamedhelmy/android-docker:31`
- `mohamedhelmy/android-docker:30`
- `mohamedhelmy/android-docker:29`
- `mohamedhelmy/android-docker:28`

### Usage:

Follow these steps to use the Docker image in your CI/CD pipeline to build and deploy Android apps to the Google Play Store:

1. **Pull the Docker Image:**

```bash
docker pull mohamedhelmy/android-docker:34
```

Build Your Android App:
Use the following command to build your Android app within the Docker container:


```bash
docker run -v /path/to/your/android/app:/root/app mohamedhelmy/android-docker:34 fastlane build 
```

Deploy Your Android App to the Google Play Store:

```bash
docker run -v /path/to/your/android/app:/root/app mohamedhelmy/android-docker:34 fastlane deploy 
```

Dockerfile:

```bash

FROM ubuntu:20.04

MAINTAINER Mohamed Helmy <helmy419@gmail.com>

# Environment variables
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US \
    LC_ALL=en_US.UTF-8 \
    DEBIAN_FRONTEND=noninteractive \
    ANDROID_HOME=/opt/android-sdk-linux \
    ANDROID_SDK_HOME=/opt/android-sdk-linux \
    ANDROID_SDK_ROOT=/opt/android-sdk-linux \
    ANDROID_SDK=/opt/android-sdk-linux \
    PATH="${PATH}:${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/cmdline-tools/tools/bin:${ANDROID_HOME}/tools/bin:${ANDROID_HOME}/build-tools/34.0.0:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/emulator:${ANDROID_HOME}/bin"

# Update and install dependencies
RUN dpkg --add-architecture i386 \
    && apt-get update -yqq \
    && apt-get install -y curl expect git libc6:i386 libgcc1:i386 libncurses5:i386 libstdc++6:i386 zlib1g:i386 openjdk-17-jdk wget unzip vim \
    && apt-get clean \
    && groupadd android \
    && useradd -d /opt/android-sdk-linux -g android android

# Copy tools and licenses
COPY tools /opt/tools
COPY licenses /opt/licenses

# Set work directory
WORKDIR /opt/android-sdk-linux

# Run entrypoint
RUN /opt/tools/entrypoint.sh built-in

# Install Android SDK components
RUN /opt/android-sdk-linux/cmdline-tools/tools/bin/sdkmanager "cmdline-tools;latest" \
    && /opt/android-sdk-linux/cmdline-tools/tools/bin/sdkmanager "build-tools;34.0.0" \
    && /opt/android-sdk-linux/cmdline-tools/tools/bin/sdkmanager "platform-tools" \
    && /opt/android-sdk-linux/cmdline-tools/tools/bin/sdkmanager "platforms;android-34" \
    && /opt/android-sdk-linux/cmdline-tools/tools/bin/sdkmanager "system-images;android-34;google_apis;x86_64"

# Install FastLane dependencies
RUN apt install ruby-full build-essential -y \
    && gem install fastlane -NV \
    && fastlane --version

# Clean up
RUN apt-get autoremove -y \
    && apt-get clean

# Default command
CMD ["/opt/tools/entrypoint.sh", "built-in"]

```



### Example Fastfile:
```ruby
default_platform(:android)

platform :android do
  lane :build do
    # Your build steps here
    gradle(
      task: "assemble",
      build_type: "Release"
    )
  end

  lane :deploy do
    # Your deployment steps here
    supply(
      track: 'internal',
      apk: 'app/build/outputs/apk/release/app-release.apk'
    )
  end
end
```
### Example .gitlab-ci.yml:
```yaml

image: mohamedhelmy/android-docker:34

stages:
  - build
  - deploy

build:
  stage: build
  script:
    - fastlane build

deploy:
  stage: deploy
  script:
    - fastlane deploy
```

### Example of Using This Image in a CI/CD Pipeline (GitHub Actions)
```yaml
name: Android CI/CD

on: [push]

jobs:
  build:
    name: Build and Deploy
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v2

    - name: Pull Docker Image
      run: docker pull mohamedhelmy/android-docker:34

    - name: Run Docker Container
      run: docker run -v ${{ github.workspace }}:/workspace mohamedhelmy/android-docker:34

    - name: Build Android App
      run: |
        cd /workspace
        fastlane build

    - name: Deploy Android App to Google Play Store
      run: |
        cd /workspace
        fastlane deploy


```
## License:
This Docker image is licensed under the [MIT] License.
