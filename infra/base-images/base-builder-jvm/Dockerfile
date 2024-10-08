# Copyright 2021 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
################################################################################

FROM gcr.io/oss-fuzz-base/base-builder

ENV JAVA_HOME /usr/lib/jvm/java-17-openjdk-amd64
ENV JAVA_15_HOME /usr/lib/jvm/java-15-openjdk-amd64
ENV JVM_LD_LIBRARY_PATH $JAVA_HOME/lib/server
ENV PATH $PATH:$JAVA_HOME/bin
ENV JAZZER_API_PATH "/usr/local/lib/jazzer_api_deploy.jar"
ENV JAZZER_JUNIT_PATH "/usr/local/bin/jazzer_junit.jar"

RUN install_java.sh

RUN chmod 777 /usr/local/bin && chmod 777 /usr/local/lib

RUN useradd -m jazzer_user

USER jazzer_user

WORKDIR $SRC

RUN git clone https://github.com/CodeIntelligenceTesting/jazzer && \
    cd jazzer && \
    git checkout aa376ca3c0255b9cf04e1fc79fcf1fe5c63b2376

WORKDIR $SRC/jazzer

RUN echo "build --java_runtime_version=local_jdk_17" >> .bazelrc \
    && echo "build --cxxopt=-stdlib=libc++" >> .bazelrc \
    && echo "build --linkopt=-lc++" >> .bazelrc

RUN bazel build \
    //src/main/java/com/code_intelligence/jazzer:jazzer_standalone_deploy.jar \
    //deploy:jazzer-api \
    //deploy:jazzer-junit \
    //launcher:jazzer

RUN cp $(bazel cquery --output=files //src/main/java/com/code_intelligence/jazzer:jazzer_standalone_deploy.jar) /usr/local/bin/jazzer_agent_deploy.jar && \
    cp $(bazel cquery --output=files //launcher:jazzer) /usr/local/bin/jazzer_driver && \
    cp $(bazel cquery --output=files //deploy:jazzer-api) $JAZZER_API_PATH && \
    cp $(bazel cquery --output=files //deploy:jazzer-junit) $JAZZER_JUNIT_PATH

USER root

RUN rm -rf /home/jazzer_user/.cache/bazel /home/jazzer_user/.cache/bazelisk && \
    rm -rf $SRC/jazzer

RUN chmod 755 /usr/local/bin && chmod 755 /usr/local/lib

WORKDIR $SRC
