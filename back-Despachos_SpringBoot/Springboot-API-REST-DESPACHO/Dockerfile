# ==========================================
# ETAPA 1: Construcción (Build Stage)
# ==========================================
FROM eclipse-temurin:17-jdk-alpine AS builder

WORKDIR /app

# Copiamos los archivos de Maven
COPY .mvn/ .mvn/
COPY mvnw pom.xml ./

# Damos permisos de ejecución al wrapper de Maven
RUN chmod +x ./mvnw

# Descargamos dependencias para aprovechar la caché de Docker
RUN ./mvnw dependency:go-offline

# Copiamos el código fuente
COPY src ./src

# Compilamos el proyecto saltando los tests para mayor rapidez
RUN ./mvnw clean package -DskipTests

# ==========================================
# ETAPA 2: Producción (Production Stage)
# ==========================================
FROM eclipse-temurin:17-jre-alpine AS production

WORKDIR /app

# REQUISITO DE RÚBRICA: Usuario no root
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring

# Copiamos el JAR compilado desde la etapa 1
COPY --from=builder /app/target/*.jar app.jar

# Exponemos el puerto exacto definido en el application.properties
EXPOSE 8081

# Comando para iniciar la API
ENTRYPOINT ["java", "-jar", "app.jar"]