FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package -DskipTests

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/transactionhistory-1.0.jar app.jar
RUN adduser -D appuser
USER appuser
EXPOSE 8084
CMD ["java" , "-jar" , "app.jar"]
