# Stage 1: Build application with Gradle
FROM eclipse-temurin:21-jdk-alpine AS builder
WORKDIR /app

# Copy Gradle files
COPY gradlew .
COPY gradle ./gradle
COPY build.gradle settings.gradle ./

# Fix Windows CRLF line endings and grant execute permission
RUN sed -i 's/\r$//' gradlew && chmod +x gradlew

# Cache dependencies
RUN ./gradlew dependencies --no-daemon || true

# Build bootable JAR package
COPY src ./src
RUN ./gradlew bootJar -x test --no-daemon

# Stage 2: Runtime image using lightweight JRE 21
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app

# Copy built JAR from builder stage
COPY --from=builder /app/build/libs/*.jar app.jar

# Expose default port
EXPOSE 8080

# Run the Spring Boot application
ENTRYPOINT ["java", "-jar", "app.jar"]
