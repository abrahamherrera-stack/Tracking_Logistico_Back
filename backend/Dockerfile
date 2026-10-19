# Etapa 1: Compilación usando el JDK oficial de Temurin 25 y tu wrapper mvnw
FROM eclipse-temurin:25-jdk-noble AS build
WORKDIR /app

# Copiar configuración de Maven y el ejecutable del wrapper
COPY .mvn .mvn
COPY mvnw pom.xml ./
RUN chmod +x mvnw

# Descargar dependencias para aprovechar caché
RUN ./mvnw dependency:go-offline -B

# Copiar el código fuente y empaquetar el JAR
COPY src src
RUN ./mvnw clean package -DskipTests

# Etapa 2: Imagen final liviana de ejecución (JRE)
FROM eclipse-temurin:25-jre-noble
WORKDIR /app

# Crear usuario seguro sin privilegios de root
RUN addgroup --system spring && adduser --system spring --ingroup spring
USER spring:spring

# Copiar el ejecutable generado en la etapa anterior
COPY --from=build /app/target/*.jar app.jar

EXPOSE 8080
ENV PORT=8080

ENTRYPOINT ["sh", "-c", "java -Dserver.port=${PORT} -jar app.jar"]
