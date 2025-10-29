# =======================
# 🧱 1. Build Stage
# =======================
FROM gradle:8.10.2-jdk21 AS builder
WORKDIR /app

# Gradle 캐시 최적화
COPY build.gradle settings.gradle ./
COPY gradle ./gradle
RUN ./gradlew dependencies --no-daemon || true

# 실제 코드 복사 및 빌드
COPY . .
RUN ./gradlew clean bootJar --no-daemon

# =======================
# ⚙️ 2. Extract Layers
# =======================
FROM eclipse-temurin:21-jdk-jammy AS extractor
WORKDIR /app
COPY --from=builder /app/build/libs/*.jar app.jar
RUN java -Djarmode=layertools -jar app.jar extract

# =======================
# 🚀 3. Run Stage
# =======================
FROM eclipse-temurin:21-jdk-jammy
WORKDIR /app

# 레이어별로 분리된 파일 복사
COPY --from=extractor /app/dependencies/ ./dependencies/
COPY --from=extractor /app/snapshot-dependencies/ ./snapshot-dependencies/
COPY --from=extractor /app/spring-boot-loader/ ./spring-boot-loader/
COPY --from=extractor /app/application/ ./application/

EXPOSE 8080
ENTRYPOINT ["java", "-cp", "spring-boot-loader:dependencies:application", "org.springframework.boot.loader.launch.JarLauncher"]

