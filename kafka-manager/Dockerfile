## =====================================================
## You are free to re-use or distributed this dockerfile
## =====================================================

FROM        openjdk:8-jdk-alpine AS builder

ENV         KAFKA_MANAGER_VERSION=1.3.3.22

RUN         apk update \
                && apk add bash \
                && cd /tmp \
                && wget "https://github.com/yahoo/kafka-manager/archive/${KAFKA_MANAGER_VERSION}.tar.gz" \
                && tar -xvf "${KAFKA_MANAGER_VERSION}.tar.gz" \
                && cd kafka-manager-${KAFKA_MANAGER_VERSION} \
                && echo 'scalacOptions ++= Seq("-Xmax-classfile-name", "200")' >> build.sbt \
                && ./sbt clean dist \
                && mkdir -p builded \
                && mkdir -p /opt/kafka-manager \
                && unzip -d builded target/universal/kafka-manager-${KAFKA_MANAGER_VERSION}.zip \
                && mv builded/kafka-manager-${KAFKA_MANAGER_VERSION} /opt/kafka-manager

FROM        openjdk:8-jre-alpine

LABEL       App="Kafka Manager" \
            Maintainer="Fredy Samuel B. Tarigan <fredy@tarigan.id>"

ENV         TIMEZONE=Asia/Jakarta

COPY        --from=builder /opt/kafka-manager /opt/kafka-manager

WORKDIR     /opt/kafka-manager

RUN         apk update \
                && apk add bash curl tzdata \
                && cp "/usr/share/zoneinfo/${TIMEZONE}" /etc/localtime \
                && addgroup -S kafka \
                && adduser -S kafka -G kafka \
                && chown -R kafka:kafka /opt/kafka-manager

USER        kafka

EXPOSE      9000
ENTRYPOINT  ["./bin/kafka-manager", "-Dconfig.file=conf/application.conf"]