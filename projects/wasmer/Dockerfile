# Copyright 2022 Google LLC
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

FROM gcr.io/oss-fuzz-base/base-builder-rust

RUN apt-get update && apt-get install -y make autoconf automake libtool \
   zlib1g-dev libffi-dev build-essential \
   clang-12 llvm-12 llvm-12-dev llvm-12-tools

RUN git clone --depth 1 https://github.com/wasmerio/wasmer wasmer

WORKDIR wasmer

COPY *.rs fuzz/fuzz_targets/
COPY build.sh default.options $SRC/