FROM maven:3.9-eclipse-temurin-17-alpine AS builder
# задаём переменную VERSION, которая будет использоваться для сборки проекта
ARG VERSION=1.0.0
ENV VERSION=${VERSION}

WORKDIR /usr/src/app
COPY ./src ./src
COPY pom.xml ./

RUN mvn package -DskipTests

# release
FROM eclipse-temurin:17-jre-alpine-3.22
ARG VERSION=1.0.0
ENV VERSION=${VERSION}

WORKDIR /app

# создаём пользователя jaruser
RUN addgroup --system jaruser \
    && adduser -S -s /bin/false -G jaruser jaruser -D -H \
    && apk add --no-cache dumb-init==1.2.5-r3 curl \
    && mkdir -p /logs/reports \
    && chown -R jaruser:jaruser /logs

COPY --chown=jaruser:jaruser \
     --from=builder /usr/src/app/target/sausage-store-${VERSION}.jar ./sausage-store.jar

EXPOSE 8080

# приложение будет запускаться под пользователем jaruser
USER jaruser

ENTRYPOINT ["dumb-init", "java", "-jar", "-Dmyserver.bindPort=8080", "./sausage-store.jar"]