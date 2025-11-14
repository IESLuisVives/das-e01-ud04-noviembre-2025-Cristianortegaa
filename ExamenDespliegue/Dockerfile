FROM gradle:jdk25-alpine AS build

WORKDIR /app

COPY build.gradle.kts .
COPY gradlew .
COPY gradle gradle
COPY src src

ARG DOCKER_HOST_ARG=tcp://host.docker.internal:2375
ENV DOCKER_HOST=$DOCKER_HOST_ARG

RUN./gradlew build javadoc jacocoTestReport

FROM nginx:latest AS test

WORKDIR /app

RUN rm -rf /usr/share/nginx/html/*

COPY --from=build /app/build/reports/tests/test/usr/share/nginx/html .

FROM nginx:latest AS jacoco

WORKDIR /app

RUN rm -rf /usr/share/nginx/html/*

COPY --from=build /app/build/reports/jacoco/test/html/usr/share/nginx/html .

FROM httpd:latest AS docs

WORKDIR /app

RUN echo 'Include conf/httpd-auth.conf' >> /usr/local/apache2/conf/httpd.conf

COPY --from=build /app/build/docs/javadoc/usr/local/apache2/htdocs .
FROM eclipse-temurin:25-jre-alpine AS run

WORKDIR /app

COPY --from=build /app/build/libs/*SNAPSHOT.jar/app/nombreApp.jar .

ENTRYPOINT["java", "-jar", "/app/nombreApp.jar"]
