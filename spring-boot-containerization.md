# Spring Boot Microservice Containerization Guide

## Table of Contents
1. [Introduction to Containerization](#introduction-to-containerization)
2. [Docker Image Layers](#docker-image-layers)
3. [Configuration Layer Management](#configuration-layer-management)
4. [Platform Base Image](#platform-base-image)
5. [Spring Boot Containerization Process](#spring-boot-containerization-process)
6. [Dockerfile Best Practices](#dockerfile-best-practices)
7. [Multi-Stage Build Implementation](#multi-stage-build-implementation)
8. [Runtime Configuration Injection](#runtime-configuration-injection)
9 Checks. [Health and Scripts](#health-checks-and-scripts)
10. [Environment-Specific Deployments](#environment-specific-deployments)

---

## Introduction to Containerization

Containerization is a lightweight form of virtualization that allows you to package an application with all its dependencies into a standardized unit called a container. Unlike traditional deployment on specific machines, containers provide process isolation while sharing the host OS kernel, making them more efficient than traditional virtual machines.

### Benefits of Containerization

- **Portability**: Containers run consistently across any environment that supports Docker
- **Isolation**: Each container runs in its own isolated process space
- **Scalability**: Easy to create multiple copies of the same application
- **Reproducibility**: Same behavior guaranteed across all environments
- **Resource Efficiency**: Lower overhead compared to full VMs
- **Rapid Deployment**: Containers start in seconds, not minutes
- **Version Control**: Images can be versioned and tagged
- **Rollback Capability**: Easy rollback to previous versions

### Why Containerization for Microservices?

Microservices architecture benefits significantly from containerization because:
- Each microservice can be developed, deployed, and scaled independently
- Different services can use different technology stacks
- Consistent deployment pipeline across all services
- Easy integration with orchestration platforms like Kubernetes
- Improved fault isolation
- Better resource utilization

---

## Docker Image Layers

A Docker image consists of multiple read-only layers that together form the complete filesystem for a container. Understanding these layers is crucial for building efficient and maintainable containerized applications.

### Layer 1: Operating System Layer

The OS layer forms the foundation of any Docker image. This layer contains the minimal necessary components of the host operating system required to run your application.

**Key Considerations:**
- Choose based on company standards and security requirements
- Common choices: Alpine Linux (smallest), Ubuntu, Debian, CentOS
- Alpine is recommended for Java applications due to small footprint (~5MB)
- Must include necessary system utilities and libraries
- Consider CVE scanning and security updates

**Example OS Selection:**
```dockerfile
# Alpine Linux - Recommended for minimal footprint
FROM eclipse-temurin:17-jre-alpine

# Or Ubuntu for more compatibility
FROM ubuntu:22.04

# Or Amazon Linux for AWS deployments
FROM amazoncorretto:17-alpine
```

### Layer 2: Framework Layer

The framework layer contains all runtime dependencies required by your application. For Spring Boot microservices, this primarily includes the Java Runtime Environment (JRE).

**Components Needed:**
- JRE/JDK of specific version
- Required system libraries
- Security certificates
- Fonts and locale data (if applicable)
- Timezone data

**Framework Layer Best Practices:**
- Use specific versions (not 'latest') for reproducibility
- Choose JRE over JDK when possible to reduce image size
- Consider using slim or alpine variants
- Pin versions for security and stability
- Document all framework dependencies

**Example:**
```dockerfile
# Using Eclipse Temurin (OpenJDK distribution)
FROM eclipse-temurin:17-jre-alpine

# Verify Java version
RUN java -version
```

### Layer 3: Application Layer

The application layer contains your built artifact - the actual executable code of your microservice.

**For Spring Boot Applications:**
- JAR file (Spring Boot executable JAR)
- WAR file (traditional deployment)
- Dependencies and libraries
- Application resources (templates, static files)

**Application Layer Characteristics:**
- Changes most frequently (every build)
- Should be at the top of Dockerfile for caching
- Must be properly versioned
- Should include checksum verification
- Consider multi-stage builds to separate build and runtime

**Example:**
```dockerfile
# Copy the built JAR file
COPY target/my-spring-boot-app.jar /app.jar

# Verify the artifact exists
RUN ls -lh /app.jar
```

### Layer 4: Script Layer

The script layer contains shell scripts and other executable files needed for application lifecycle management.

**Common Scripts Needed:**
- Startup scripts
- Health check scripts
- Graceful shutdown scripts
- Pre-startup initialization scripts
- Logging configuration scripts
- Backup and maintenance scripts

**Example Startup Script:**
```bash
#!/bin/sh
# Application startup script

# Set default JVM options
JVM_OPTS="-Xmx512m -Xms256m"

# Configure logging
export LOG_DIR=/var/log/myapp
mkdir -p $LOG_DIR

# Start the application
exec java $JVM_OPTS -jar /app.jar \
    --spring.config.location=/config/ \
    --server.port=8080 \
    >> $LOG_DIR/app.log 2>&1
```

---

## Configuration Layer Management

The configuration layer is critical for maintaining environment-specific settings without rebuilding images. This layer is injected at runtime rather than baked into the image.

### Why Configuration Should Be Runtime-Injected

**Problems with Baking Configurations:**
- Same image for all environments would require different database connections
- Security risk: production credentials in shared images
- No flexibility for configuration changes without rebuild
- Violates "build once, deploy anywhere" principle
- Difficult to maintain environment-specific settings

**Benefits of Runtime Configuration:**
- Single image across all environments
- Enhanced security (credentials not in image)
- Faster deployment cycles
- Easier configuration management
- Better audit trail for configuration changes

### Configuration Types

**Environment-Specific (Runtime):**
- Database connection strings
- API endpoints for other services
- Feature flags
- Performance tuning parameters
- Logging levels

**Application-Specific (Image):**
- Framework defaults
- Application metadata
- Static configuration
- Default timeouts and limits

### Configuration Injection Methods

**Method 1: Environment Variables**
```yaml
# docker-compose.yml
services:
  myapp:
    image: my-spring-boot-app:latest
    environment:
      - SPRING_PROFILES_ACTIVE=production
      - DATABASE_URL=jdbc:postgresql://db:5432/mydb
      - CACHE_HOST=redis:6379
```

**Method 2: Config Files via Volume Mounts**
```yaml
# docker-compose.yml
services:
  myapp:
    image: my-spring-boot-app:latest
    volumes:
      - ./config/production:/config:ro
      - ./secrets:/run/secrets:ro
```

**Method 3: Kubernetes ConfigMaps and Secrets**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
data:
  application.properties: |
    spring.datasource.url=jdbc:postgresql://db:5432/mydb
    server.port=8080
---
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secrets
type: Opaque
stringData:
  spring.datasource.username: dbuser
  spring.datasource.password: dbpassword
```

### Configuration Directory Structure

```
/config/
├── application.properties
├── application-{profile}.properties
├── logback.xml
└── certificates/
    ├── truststore.jks
    └── keystore.jks
```

---

## Platform Base Image

A platform base image is a pre-built Docker image that contains the OS and framework layers, serving as the foundation for application-specific images.

### Purpose of Platform Base Images

**Reusability:**
- Single base image used across multiple microservices
- Consistent runtime environment across the organization
- Reduces duplication of effort
- Easier to maintain and update

**Centralized Management:**
- Security updates managed centrally
- Version control at platform level
- Compliance and auditing simplified
- Performance optimization at platform level

**Technology Support:**
- Multiple Java versions supported
- Different frameworks for different services
- Language-specific runtimes
- Database client libraries

### Platform Base Image Structure

```
Platform Base Image
├── OS Layer (Alpine/Ubuntu)
├── Framework Layer (JRE/JDK)
├── Common Utilities
├── Security Updates
└── Performance Tuning
```

### Creating Platform Base Images

**Example Platform Base Image for Java 17:**
```dockerfile
# platform-base-java17.Dockerfile
FROM eclipse-temurin:17-jre-alpine

# Install common utilities
RUN apk add --no-cache \
    curl \
    wget \
    jq \
    bash \
    && rm -rf /var/cache/apk/*

# Configure timezone
ENV TZ=UTC
RUN apk add --no-cache tzdata

# Add non-root user for security
RUN addgroup -g 1000 app && \
    adduser -u 1000 -G app -s /bin/sh -D app

# Set working directory
WORKDIR /home/app

# Create necessary directories
RUN mkdir -p /config /logs /tmp

# Set permissions
RUN chown -R app:app /home/app /config /logs /tmp

# Default JVM options
ENV JAVA_OPTS="-Xmx512m -Xms256m"

USER app

CMD ["sh"]
```

### Using Platform Base Images

```dockerfile
# application.Dockerfile
# syntax=docker/dockerfile:1
FROM myregistry/platform-base-java17:latest

# Copy application artifacts
COPY target/*.jar /app.jar

# Copy startup scripts
COPY scripts/ /usr/local/bin/

# Make scripts executable
RUN chmod +x /usr/local/bin/*

# Set ownership
RUN chown app:app /app.jar

# Expose application port
EXPOSE 8080

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8080/actuator/health || exit 1

# Run as non-root user
USER app

# Startup command
ENTRYPOINT ["sh", "/usr/local/bin/start.sh"]
```

---

## Spring Boot Containerization Process

This section provides a step-by-step guide to containerizing a Spring Boot microservice.

### Step 1: Prepare the Spring Boot Application

**Ensure your Spring Boot application is ready:**

1. **Configure Spring Boot Maven/Gradle plugin:**
```xml
<!-- pom.xml -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>3.2.0</version>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

2. **Add actuator for health checks:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

3. **Configure application properties:**
```properties
# application.properties
server.port=8080
spring.application.name=my-microservice

# Actuator configuration
management.endpoints.web.exposure.include=health,info,metrics
management.endpoint.health.show-details=when_authorized
```

### Step 2: Build the Application

```bash
# Build the Spring Boot application
mvn clean package -DskipTests

# Verify the JAR file was created
ls -lh target/*.jar
```

### Step 3: Create the Dockerfile

**Minimal Dockerfile:**
```dockerfile
FROM eclipse-temurin:17-jre-alpine

# Create non-root user for security
RUN addgroup -g 1000 app && \
    adduser -u 1000 -G app -s /bin/sh -D app

# Set working directory
WORKDIR /home/app

# Copy the built JAR
COPY target/my-microservice-1.0.0.jar app.jar

# Change ownership
RUN chown app:app app.jar

# Expose port
EXPOSE 8080

# Run as non-root
USER app

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8080/actuator/health || exit 1

# Run application
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Step 4: Build and Test the Docker Image

```bash
# Build the Docker image
docker build -t my-microservice:latest .

# Run the container locally
docker run -p 8080:8080 \
    -e SPRING_PROFILES_ACTIVE=local \
    --name my-microservice-test \
    my-microservice:latest

# Check logs
docker logs my-microservice-test

# Stop the container
docker stop my-microservice-test
docker rm my-microservice-test
```

---

## Dockerfile Best Practices

### 1. Use Specific Base Image Tags

**Bad Practice:**
```dockerfile
FROM openjdk:latest
```

**Good Practice:**
```dockerfile
FROM eclipse-temurin:17-jre-alpine
```

### 2. Use Multi-Stage Builds

Multi-stage builds reduce final image size and improve security by separating build and runtime environments.

```dockerfile
# syntax=docker/dockerfile:1
# Stage 1: Build
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /build
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# Stage 2: Runtime
FROM eclipse-temurin:17-jre-alpine AS runtime
WORKDIR /home/app

# Create non-root user
RUN addgroup -g 1000 app && \
    adduser -u 1000 -G app -s /bin/sh -D app

# Copy artifact from builder
COPY --from=builder /build/target/*.jar app.jar

# Change ownership
RUN chown app:app app.jar

USER app

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 3. Order Dockerfile Instructions for Caching

Place instructions that change less frequently at the top and those that change often at the bottom.

```dockerfile
# Cacheable layers first
FROM eclipse-temurin:17-jre-alpine
RUN addgroup -g 1000 app && \
    adduser -u 1000 -G app -s /bin/sh -D app

# Less frequently changed
WORKDIR /home/app
RUN mkdir -p /config /logs /tmp

# Application changes frequently - put last
COPY target/*.jar app.jar
```

### 4. Minimize Image Layers

Combine related RUN instructions to reduce the number of layers.

**Bad Practice:**
```dockerfile
RUN apk add curl
RUN apk add wget
RUN apk add jq
RUN rm -rf /var/cache/apk/*
```

**Good Practice:**
```dockerfile
RUN apk add --no-cache curl wget jq && \
    rm -rf /var/cache/apk/*
```

### 5. Use .dockerignore

Create a `.dockerignore` file to exclude unnecessary files from the build context:

```
# .dockerignore
.git
.gitignore
target/
src/test/
*.md
*.log
.dockerignore
Dockerfile
.docker/
```

### 6. Never Run as Root

```dockerfile
# Create and use non-root user
RUN addgroup -g 1000 app && \
    adduser -u 1000 -G app -s /bin/sh -D app

USER app
```

### 7. Use Proper Labels

```dockerfile
LABEL maintainer="team@example.com" \
      version="1.0.0" \
      description="My Spring Boot Microservice" \
      org.opencontainers.image.source="https://github.com/myorg/my-microservice"
```

### 8. Include Health Checks

```dockerfile
# Simple health check using curl
HEALTHCHECK --interval=30s --timeout=10s --start-period=10s --retries=3 \
    CMD curl -f http://localhost:8080/actuator/health || exit 1

# Alternative using wget
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:8080/actuator/health || exit 1

# For Alpine without curl/wget
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:8080/actuator/health || exit 1
```

---

## Multi-Stage Build Implementation

Multi-stage builds are essential for creating production-ready images with minimal size and attack surface.

### Complete Multi-Stage Dockerfile

```dockerfile
# syntax=docker/dockerfile:1
# ===========================================
# Stage 1: Builder - Compile and test
# ===========================================
FROM maven:3.9-eclipse-temurin-17 AS builder

# Set working directory
WORKDIR /build

# Copy Maven wrapper and pom.xml first for caching
COPY mvnw .
COPY pom.xml .

# Download dependencies (cached if pom.xml unchanged)
RUN ./mvnw dependency:go-offline -B

# Copy source code
COPY src ./src

# Build and package the application
RUN ./mvnw clean package -DskipTests -B

# ===========================================
# Stage 2: Runtime - Minimal production image
# ===========================================
FROM eclipse-temurin:17-jre-alpine AS runtime

# Labels
LABEL maintainer="platform-team@example.com" \
      version="${project.version}" \
      description="Spring Boot Microservice" \
      org.opencontainers.image.source="https://github.com/myorg/my-microservice"

# Install runtime dependencies
RUN apk add --no-cache \
    curl \
    && rm -rf /var/cache/apk/*

# Create non-root user
RUN addgroup -g 1000 app && \
    adduser -u 1000 -G app -s /bin/sh -D app

# Create directory structure
RUN mkdir -p /home/app /config /logs /tmp && \
    chown -R app:app /home/app /config /logs /tmp

# Set working directory
WORKDIR /home/app

# Copy artifact from builder stage
COPY --from=builder /build/target/*.jar app.jar

# Copy configuration files
COPY --from=builder /build/config/ /config/

# Change ownership
RUN chown -R app:app /home/app

# Expose application port
EXPOSE 8080

# Set environment variables
ENV JAVA_OPTS="-Xmx512m -Xms256m" \
    SPRING_PROFILES_ACTIVE="" \
    APP_HOME=/home/app

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=15s --retries=3 \
    CMD curl -f http://localhost:8080/actuator/health || exit 1

# Switch to non-root user
USER app

# Run the application
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar --spring.config.location=/config/"]
```

### Build Arguments

```dockerfile
# Build arguments for customization
ARG PROJECT_VERSION=1.0.0
ARG BUILD_NUMBER=001

LABEL version="${PROJECT_VERSION}" \
      build.number="${BUILD_NUMBER}"
```

---

## Runtime Configuration Injection

This section covers various methods for injecting configuration at runtime.

### Environment Variables

**Setting Environment Variables:**
```dockerfile
ENV SPRING_PROFILES_ACTIVE=production
ENV SERVER_PORT=8080
ENV JAVA_OPTS="-Xmx1g -Xms512m"
```

**Overriding at Runtime:**
```bash
docker run -e SPRING_PROFILES_ACTIVE=staging my-microservice:latest
```

### ConfigMap-style Configuration

**Application Configuration File:**
```properties
# application.properties
spring.application.name=${APP_NAME:my-microservice}
server.port=${SERVER_PORT:8080}

# Database configuration
spring.datasource.url=${DATABASE_URL}
spring.datasource.username=${DATABASE_USERNAME}
spring.datasource.password=${DATABASE_PASSWORD}
spring.datasource.driver-class-name=org.postgresql.Driver

# Redis configuration
spring.data.redis.host=${REDIS_HOST}
spring.data.redis.port=${REDIS_PORT:6379}

# Logging
logging.level.root=${LOG_LEVEL:INFO}
logging.level.com.mycompany=${LOG_LEVEL_APP:DEBUG}
```

### Volume Mounts for Configuration

**Docker Compose:**
```yaml
version: '3.8'
services:
  my-microservice:
    image: my-microservice:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config/production:/config:ro
      - ./secrets:/run/secrets:ro
      - app-logs:/home/app/logs
    environment:
      - SPRING_PROFILES_ACTIVE=production
    secrets:
      - db-password
    deploy:
      resources:
        limits:
          cpus: '1'
          memory: 1G
        reservations:
          cpus: '0.5'
          memory: 512M

volumes:
  app-logs:

secrets:
  db-password:
    file: ./secrets/db-password.txt
```

### Kubernetes Configuration

**ConfigMap:**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
  labels:
    app: myapp
data:
  application.properties: |
    spring.datasource.url=jdbc:postgresql://postgres:5432/myapp
    spring.datasource.username=myapp_user
    server.port=8080
    logging.level.root=INFO
  application.yml: |
    spring:
      profiles:
        active: production
    management:
      endpoints:
        web:
          exposure:
            include: health,info,metrics
---
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secrets
type: Opaque
stringData:
  spring.datasource.password: your-secure-password
  jwt.secret: your-jwt-secret-key
```

**Deployment with ConfigMap:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: my-microservice:latest
        ports:
        - containerPort: 8080
        envFrom:
        - configMapRef:
            name: myapp-config
        - secretRef:
            name: myapp-secrets
        volumeMounts:
        - name: config-volume
          mountPath: /config
          readOnly: true
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
      volumes:
      - name: config-volume
        configMap:
          name: myapp-config
```

---

## Health Checks and Scripts

Proper health checks and lifecycle scripts are essential for containerized applications.

### Startup Script

**start.sh:**
```bash
#!/bin/sh
set -e

# Configuration
APP_HOME=${APP_HOME:-/home/app}
CONFIG_DIR=${CONFIG_DIR:-/config}
LOG_DIR=${LOG_DIR:-$APP_HOME/logs}
JAR_FILE=${JAR_FILE:-app.jar}

# JVM Options
JAVA_OPTS=${JAVA_OPTS:-"-Xmx512m -Xms256m"}

# Create directories
mkdir -p "$LOG_DIR" "$CONFIG_DIR"

# Set Java system properties
JAVA_OPTS="$JAVA_OPTS -Dlogging.file.path=$LOG_DIR"
JAVA_OPTS="$JAVA_OPTS -Dlogging.file.name=$LOG_DIR/app.log"

# Export environment
export SPRING_CONFIG_LOCATION=${CONFIG_DIR}/
export SPRING_PROFILES_ACTIVE=${SPRING_PROFILES_ACTIVE:-default}

# Print configuration
echo "Starting application..."
echo "Profile: $SPRING_PROFILES_ACTIVE"
echo "Config: $SPRING_CONFIG_LOCATION"
echo "Log: $LOG_DIR"

# Trap SIGTERM for graceful shutdown
shutdown() {
    echo "Received shutdown signal..."
    # Graceful shutdown logic
    exit 0
}
trap shutdown SIGTERM SIGINT

# Start the application
exec java $JAVA_OPTS -jar "$JAR_FILE" \
    --spring.config.location="$SPRING_CONFIG_LOCATION" \
    --server.port=${SERVER_PORT:-8080} \
    2>&1 &
    
APP_PID=$!

# Wait for application to start
echo "Waiting for application to start (PID: $APP_PID)..."
sleep 10

# Keep script running and forward signals
wait $APP_PID
```

### Health Check Endpoint Configuration

**Spring Boot Actuator Configuration:**
```properties
# application.properties
# Enable all health endpoints
management.endpoints.web.exposure.include=health,info,metrics,prometheus

# Configure health groups
management.endpoint.health.group.custom.include=db,redis,diskSpace
management.endpoint.health.show-details=when_authorized

# Liveness and readiness probes
management.endpoint.health.probes.enabled=true
management.health.liveness-state.enabled=true
management.health.readiness-state.enabled=true

# Custom health indicators
management.endpoint.health.enabled=true
```

**Custom Health Indicator:**
```java
import org.springframework.boot.actuate.health.Health;
import org.springframework.boot.actuate.health.HealthIndicator;
import org.springframework.stereotype.Component;

@Component
public class CustomHealthIndicator implements HealthIndicator {
    
    @Override
    public Health health() {
        // Add custom health check logic
        try {
            // Check your service dependency
            boolean isHealthy = checkServiceHealth();
            
            if (isHealthy) {
                return Health.up()
                    .withDetail("database", "connected")
                    .withDetail("timestamp", System.currentTimeMillis())
                    .build();
            } else {
                return Health.down()
                    .withDetail("database", "disconnected")
                    .withDetail("timestamp", System.currentTimeMillis())
                    .build();
            }
        } catch (Exception e) {
            return Health.down()
                .withException(e)
                .build();
        }
    }
    
    private boolean checkServiceHealth() {
        // Your health check logic
        return true;
    }
}
```

### Graceful Shutdown

**Graceful Shutdown Configuration:**
```properties
# application.properties
server.shutdown=graceful
spring.lifecycle.timeout-per-shutdown-phase=30s

# Database connection pool graceful shutdown
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=5
```

**Graceful Shutdown Script:**
```bash
#!/bin/sh
# Graceful shutdown script

APP_PID=$(pgrep -f "java.*app.jar")

if [ -n "$APP_PID" ]; then
    echo "Sending SIGTERM to PID: $APP_PID"
    kill -TERM $APP_PID
    
    # Wait for graceful shutdown
    for i in {1..30}; do
        if ! kill -0 $APP_PID 2>/dev/null; then
            echo "Application stopped gracefully"
            exit 0
        fi
        sleep 1
    done
    
    # Force kill if still running
    echo "Application did not stop gracefully, forcing..."
    kill -9 $APP_PID
    exit 1
else
    echo "No running application found"
    exit 0
fi
```

---

## Environment-Specific Deployments

This section explains how to use the same Docker image across different environments.

### Environment Matrix

| Environment | Profile | Database | Configuration Source |
|-------------|---------|----------|---------------------|
| Local | local | H2 (embedded) | local files |
| Development | dev | PostgreSQL (dev) | config/dev/ |
| Integration | integration | PostgreSQL (shared) | config/integration/ |
| QA/Testing | test | PostgreSQL (test) | config/qa/ |
| Staging | staging | PostgreSQL (staging) | config/staging/ |
| Production | prod | PostgreSQL (prod) | Kubernetes ConfigMap |

### Docker Compose for Different Environments

**docker-compose.local.yml:**
```yaml
version: '3.8'
services:
  my-microservice:
    build:
      context: .
      dockerfile: Dockerfile
    image: my-microservice:local
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=local
      - SERVER_PORT=8080
    volumes:
      - ./config/local:/config:ro
    networks:
      - local-network

networks:
  local-network:
    driver: bridge
```

**docker-compose.integration.yml:**
```yaml
version: '3.8'
services:
  my-microservice:
    image: my-microservice:${CI_COMMIT_SHA:-latest}
    environment:
      - SPRING_PROFILES_ACTIVE=integration
      - DATABASE_HOST=postgres-integration
      - DATABASE_PORT=5432
      - DATABASE_NAME=myapp_integration
      - REDIS_HOST=redis-integration
    configs:
      - source: app_config_integration
        target: /config/application.properties
    depends_on:
      postgres-integration:
        condition: service_healthy
      redis-integration:
        condition: service_started
    networks:
      - integration-network
    deploy:
      resources:
        limits:
          memory: 1G
          cpus: '0.5'

configs:
  app_config_integration:
    file: ./config/integration/application.properties

networks:
  integration-network:
    external: true
```

**docker-compose.production.yml:**
```yaml
version: '3.8'
services:
  my-microservice:
    image: my-microservice:${VERSION:-latest}
    environment:
      - SPRING_PROFILES_ACTIVE=production
    configs:
      - source: app_config_prod
        target: /config/application.properties
      - source: app_secrets
        target: /run/secrets/app-secrets
    deploy:
      mode: replicated
      replicas: 3
      resources:
        limits:
          memory: 2G
          cpus: '1'
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/actuator/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 30s

configs:
  app_config_prod:
    external: true
  app_secrets:
    external: true
```

### Deployment Pipeline

**CI/CD Pipeline for Docker:**

```yaml
# .gitlab-ci.yml
stages:
  - build
  - test
  - package
  - deploy

variables:
  DOCKER_DRIVER: overlay2
  DOCKER_TLS_CERTDIR: ""
  IMAGE_NAME: my-microservice

build:
  stage: build
  script:
    - mvn clean compile
  artifacts:
    paths:
      - target/

test:
  stage: test
  script:
    - mvn test
  artifacts:
    reports:
      junit: target/surefire-reports/*.xml

docker-build:
  stage: package
  image: docker:24
  services:
    - docker:24-dind
  script:
    - docker build -t $IMAGE_NAME:$CI_COMMIT_SHA .
    - docker tag $IMAGE_NAME:$CI_COMMIT_SHA $IMAGE_NAME:latest
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker push $IMAGE_NAME:$CI_COMMIT_SHA
    - docker push $IMAGE_NAME:latest
  only:
    - main
    - develop
    - /^release\/.*$/

deploy-integration:
  stage: deploy
  script:
    - kubectl set image deployment/myapp my-microservice=$IMAGE_NAME:$CI_COMMIT_SHA -n integration
  environment:
    name: integration
    url: https://integration.myapp.com
  only:
    - develop

deploy-staging:
  stage: deploy
  script:
    - kubectl set image deployment/myapp my-microservice=$IMAGE_NAME:$CI_COMMIT_SHA -n staging
  environment:
    name: staging
    url: https://staging.myapp.com
  when: manual
  only:
    - /^release\/.*$/

deploy-production:
  stage: deploy
  script:
    - kubectl set image deployment/myapp my-microservice=$IMAGE_NAME:$CI_COMMIT_TAG -n production
  environment:
    name: production
    url: https://myapp.com
  when: manual
  only:
    - tags
```

### Security Best Practices for Different Environments

**Development:**
- Use simple credentials
- Less restrictive network policies
- Enable detailed logging
- Debug endpoints exposed

**Production:**
- Strong credentials and secrets management
- Strict network policies
- Minimal logging verbosity
- Security headers enabled
- HTTPS/TLS enforced
- Rate limiting enabled
- No debug endpoints
- Image scanning enabled

---

## Summary

Containerizing Spring Boot microservices involves:

1. **Understanding Docker Image Layers:**
   - OS Layer: Foundation of the image
   - Framework Layer: JRE and dependencies
   - Application Layer: JAR file and resources
   - Script Layer: Startup, health, and lifecycle scripts
   - Configuration Layer: Runtime-injected settings

2. **Building Efficient Images:**
   - Use multi-stage builds
   - Minimize image size
   - Follow Dockerfile best practices
   - Use specific version tags
   - Never run as root

3. **Managing Configuration:**
   - Inject configuration at runtime
   - Use environment variables
   - Mount configuration files
   - Use secrets for sensitive data

4. **Implementing Health Checks:**
   - Configure Spring Boot Actuator
   - Add custom health indicators
   - Implement graceful shutdown
   - Set up proper liveness/readiness probes

5. **Environment-Specific Deployments:**
   - Single image, multiple environments
   - Environment-specific configuration
   - CI/CD pipeline integration
   - Security considerations per environment

By following these guidelines, you can create a robust, maintainable, and secure containerized Spring Boot microservice deployment strategy that supports modern DevOps and platform engineering practices.
