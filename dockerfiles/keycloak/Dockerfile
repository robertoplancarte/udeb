# Keycloak with keycloak-scim-outbound, which sends a realm's user changes to a
# SCIM endpoint: here, locke's /scim/v2 on each tenant's host. Keycloak has no
# SCIM client of its own, and the extension publishes no builds, so this builds
# it from source at a pinned commit, against this Keycloak version. GPL-3.0.
# To upgrade, change the versions below, then:
#   docker compose build keycloak && docker compose up -d keycloak
ARG KEYCLOAK_VERSION=26.4.7

FROM maven:3.9-eclipse-temurin-21 AS scim-outbound
ARG KEYCLOAK_VERSION
ADD https://github.com/Termindiego25/keycloak-scim-outbound.git#6514623fa4a143dcdf8d9b8af9e7bf581c7b3fc2 /src
WORKDIR /src
RUN mvn -B -q package -DskipTests -Dkeycloak.version=${KEYCLOAK_VERSION} \
 && cp target/keycloak-scim-outbound-*.jar /keycloak-scim-outbound.jar

# start-dev builds Keycloak with the providers it finds at every start
FROM quay.io/keycloak/keycloak:${KEYCLOAK_VERSION}
COPY --from=scim-outbound /keycloak-scim-outbound.jar /opt/keycloak/providers/
