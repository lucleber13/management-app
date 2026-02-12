# Multi-stage build for smaller image
FROM maven:3.9-eclipse-temurin-25 AS builder

WORKDIR /app

# Copy parent POM first
COPY pom.xml .

# Copy backend module
COPY backend/management_server/pom.xml backend/management_server/
COPY backend/management_server/src backend/management_server/src

# Build the application (skip tests for faster builds)
RUN mvn clean package -DskipTests -pl backend/management_server -am

# Runtime stage
FROM eclipse-temurin:25-jre-alpine

WORKDIR /app

# Copy the built JAR from builder stage
COPY --from=builder /app/backend/management_server/target/*.jar app.jar

# Create non-root user for security
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring

# Expose port
EXPOSE 8080

# Health check
HEALTHCHECK --interval=10s --timeout=3s --start-period=30s \
  CMD wget -qO- http://localhost:8080/actuator/health || exit 1

# Run the application
ENTRYPOINT ["java", "-jar", "app.jar"]
