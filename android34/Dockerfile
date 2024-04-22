# Maintainer information
MAINTAINER Mohamed Helmy <helmy419@gmail.com>

# Base image
FROM ubuntu:20.04

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
RUN apt install ruby-full -y \
    && gem install fastlane -NV \
    && fastlane --version

# Clean up
RUN apt-get autoremove -y \
    && apt-get clean

# Default command
CMD ["/opt/tools/entrypoint.sh", "built-in"]
