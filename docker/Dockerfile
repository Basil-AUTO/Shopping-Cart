# Build Stage
FROM maven:3.9.12-eclipse-temurin-11 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# Run Stage
FROM eclipse-temurin:11-jre-alpine
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar


# Exposing the port is good practice, though -p in docker run overrides it
EXPOSE 8070
ENTRYPOINT ["sh", "-c", "java \$JAVA_OPTS -jar app.jar"]
