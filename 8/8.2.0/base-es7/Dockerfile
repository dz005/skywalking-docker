# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
FROM adoptopenjdk/openjdk11:alpine AS build

LABEL maintainer="hanahmily@apache.org,tanjian@apache.org"

ENV SKYWALKING_HOME=/skywalking
RUN mkdir -p "${SKYWALKING_HOME}"
WORKDIR $SKYWALKING_HOME

ENV GPG_KEYS A968F6905E0ACB59E5B24C15D3D9CD50820184C2 B0801BC746F965029A1338072EF5026E70A55777 D360AB2AB20B28403270E2CBE8608938DB25E06B F5B319063296F6B8207748D58BD99F552D9F33D7 F5B319063296F6B8207748D58BD99F552D9F33D7 820232B5C115CE5D30DB03F9711C739B21E3F079
ENV SKYWALKING_VERSION=8.2.0
ENV SKYWALKING_CLI_VERSION=0.4.0
ENV SKYWALKING_SHA512 7eb4b3ed37867e0221ef9d4b4e1ab3e18acf67120be5fc5561a58b99168756efde277eeec199e4ba7a4dd8ee412dff810c1793889ab530cbbbcc906a793dbccc
ENV SKYWALKING_CLI_SHA512 a74278c61788a962bef8fde027daf5c748556fb134a8c332d7a12abcab7736511cfba6c1bb5a4cad95f2af7c60ce53cec212bb610471d2d792ce311a72bd8c14

ENV SKYWALKING_TGZ_URLS \
        https://www.apache.org/dyn/closer.cgi?action=download&filename=skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-es7-$SKYWALKING_VERSION.tar.gz \
        # if the version is outdated, we might have to pull from the dist/archive :/
	    https://www-us.apache.org/dist/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-es7-$SKYWALKING_VERSION.tar.gz \
	    https://www.apache.org/dist/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-es7-$SKYWALKING_VERSION.tar.gz \
	    https://archive.apache.org/dist/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-es7-$SKYWALKING_VERSION.tar.gz

ENV SKYWALKING_ASC_URLS \
        https://www.apache.org/dyn/closer.cgi?action=download&filename=skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-es7-$SKYWALKING_VERSION.tar.gz.asc \
        # if the version is outdated, we might have to pull from the dist/archive :/
	    https://www-us.apache.org/dist/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-es7-$SKYWALKING_VERSION.tar.gz.asc \
	    https://www.apache.org/dist/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-es7-$SKYWALKING_VERSION.tar.gz.asc \
	    https://archive.apache.org/dist/skywalking/$SKYWALKING_VERSION/apache-skywalking-apm-es7-$SKYWALKING_VERSION.tar.gz.asc

ENV SKYWALKING_CLI_TGZ_URLS \
        https://www.apache.org/dyn/closer.cgi?action=download&filename=skywalking/cli/$SKYWALKING_CLI_VERSION/skywalking-cli-$SKYWALKING_CLI_VERSION-bin.tgz \
        # if the version is outdated, we might have to pull from the dist/archive :/
	    https://www-us.apache.org/dist/skywalking/cli/$SKYWALKING_CLI_VERSION/skywalking-cli-$SKYWALKING_CLI_VERSION-bin.tgz \
	    https://www.apache.org/dist/skywalking/cli/$SKYWALKING_CLI_VERSION/skywalking-cli-$SKYWALKING_CLI_VERSION-bin.tgz \
	    https://archive.apache.org/dist/skywalking/cli/$SKYWALKING_CLI_VERSION/skywalking-cli-$SKYWALKING_CLI_VERSION-bin.tgz

ENV SKYWALKING_CLI_ASC_URLS \
        https://www.apache.org/dyn/closer.cgi?action=download&filename=skywalking/$SKYWALKING_CLI_VERSION/skywalking-cli-$SKYWALKING_CLI_VERSION-bin.tgz.asc \
        # if the version is outdated, we might have to pull from the dist/archive :/
	    https://www-us.apache.org/dist/skywalking/cli/$SKYWALKING_CLI_VERSION/skywalking-cli-$SKYWALKING_CLI_VERSION-bin.tgz.asc \
	    https://www.apache.org/dist/skywalking/cli/$SKYWALKING_CLI_VERSION/skywalking-cli-$SKYWALKING_CLI_VERSION-bin.tgz.asc \
	    https://archive.apache.org/dist/skywalking/cli/$SKYWALKING_CLI_VERSION/skywalking-cli-$SKYWALKING_CLI_VERSION-bin.tgz.asc

RUN set -eux; \
	\
	apk add --no-cache --virtual .fetch-deps \
		gnupg \
		\
		ca-certificates \
		openssl \
	; \
	\
	export GNUPGHOME="$(mktemp -d)"; \
	for key in $GPG_KEYS; do \
		gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
	done; \
	\
	success=; \
	for url in $SKYWALKING_TGZ_URLS; do \
		if wget -O skywalking.tar.gz "$url"; then \
			success=1; \
			break; \
		fi; \
	done; \
	[ -n "$success" ]; \
	\
	echo "$SKYWALKING_SHA512 *skywalking.tar.gz" | sha512sum -c -; \
	\
	success=; \
	for url in $SKYWALKING_ASC_URLS; do \
		if wget -O skywalking.tar.gz.asc "$url"; then \
			success=1; \
			break; \
		fi; \
	done; \
	[ -n "$success" ]; \
	\
	gpg --batch --verify skywalking.tar.gz.asc skywalking.tar.gz; \
	tar -xvf skywalking.tar.gz --strip-components=1; \
	rm -rf bin/; \
	rm skywalking.tar.gz*; \
	success=; \
	for url in $SKYWALKING_CLI_TGZ_URLS; do \
		if wget -O skywalking-cli-bin.tgz "$url"; then \
			success=1; \
			break; \
		fi; \
	done; \
	[ -n "$success" ]; \
	\
	echo "$SKYWALKING_CLI_SHA512 *skywalking-cli-bin.tgz" | sha512sum -c -; \
	\
	success=; \
	for url in $SKYWALKING_CLI_ASC_URLS; do \
		if wget -O skywalking-cli-bin.tgz.asc "$url"; then \
			success=1; \
			break; \
		fi; \
	done; \
	[ -n "$success" ]; \
	\
	gpg --batch --verify skywalking-cli-bin.tgz.asc skywalking-cli-bin.tgz; \
	tar -xvf skywalking-cli-bin.tgz; \
    mkdir bin; \
	mv skywalking-cli-${SKYWALKING_CLI_VERSION}-bin cli; \
	mv cli/bin/swctl-${SKYWALKING_CLI_VERSION}-linux-amd64 bin/swctl; \
	rm -rf cli/bin/; \
	chmod 755 bin/*; \
	rm skywalking-cli-bin.tgz*; \
	command -v gpgconf && gpgconf --kill all || :; \
	rm -rf "$GNUPGHOME"; \
    apk del .fetch-deps

FROM adoptopenjdk/openjdk11:alpine

COPY --from=build /skywalking /skywalking

RUN apk add --no-cache \
    bash

