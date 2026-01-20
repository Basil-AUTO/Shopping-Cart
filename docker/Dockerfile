# --- Stage 1: Build ---
# We use a Maven image with JDK 11 to match your Jenkins tools
FROM maven:3.9.12-eclipse-temurin-11 AS build

# Set working directory
WORKDIR /app

# Copy pom.xml first to leverage Docker cache
# If pom.xml doesn't change, this layer is cached
COPY pom.xml .

# Download dependencies (this will be cached if pom.xml is unchanged)
RUN mvn dependency:go-offline -B

# Copy the source code
COPY src ./src

# Compile and package the application
# We skip tests here because CI pipelines usually handle tests separately
# or we run them inside the container later if preferred.
RUN mvn clean package -DskipTests

# --- Stage 2: Run ---
# We use a lighter JRE image for the final artifact
FROM eclipse-temurin:11-jre-alpine

# Install tzdata (optional, often needed for Java timezones) and curl
RUN apk add --no-cache tzdata curl

# Set working directory
WORKDIR /app

# Copy the jar file from the build stage
COPY --from=build /app/target/*.jar app.jar

# Expose the port
EXPOSE 8070

# Set environment variables for performance
ENV JAVA_OPTS="-Xms128m -Xmx512m -Djava.security.egd=file:/dev/./urandom -Djava.io.tmpdir=/tmp"

# Entry point
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]

