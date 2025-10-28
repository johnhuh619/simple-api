# Build Stage (멀티스테이지 빌드)
FROM gradle:8.10.2-jdk21 AS Builder
WORKDIR /app

# Gradle 캐시 최적화
COPY build.gradle settings.gradle ./
COPY gradle ./gradle
RUN gradle dependecies --no-daemon || true

# 실제 코드 복사 및 빌드
COPY . .
RUN ./gradlew bootJar --no-daemon

# Run Stage (경량 런타임)
FROM eclipse-temurin:21-jdk-jammy
WORKDIR /app

# 빌드 산출무 복사
COPY --from=builder /app/build/libs/*.jar app.jar

# Spring Boot Layered JAR 구조 지원 (3.5+)
RUN java -Djarmode=layertools -jar app.jar extract

# 애플리케이션 실행
ENTRYPOINT ["java", "org.springframework.boot.loader.launch.JarLauncher"]
