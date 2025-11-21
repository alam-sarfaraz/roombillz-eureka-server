# ----------- Stage 1: Build -----------
FROM eclipse-temurin:21-jdk AS builder

WORKDIR /app
COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .
RUN chmod +x mvnw && ./mvnw dependency:go-offline -B

COPY src ./src
RUN ./mvnw clean package -DskipTests -B

# ----------- Stage 2: Runtime -----------
FROM eclipse-temurin:21-jre

RUN useradd -ms /bin/bash appuser || true
USER appuser

WORKDIR /home/appuser/app

COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8761

HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD curl -f http://localhost:8761 || exit 1

ENTRYPOINT ["java", "-jar", "app.jar"]
