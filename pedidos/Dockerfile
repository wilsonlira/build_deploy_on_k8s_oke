FROM maven:3.9.9-eclipse-temurin-17-focal AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre-focal
WORKDIR /app
COPY --from=build /app/target/pedidos-0.0.1-SNAPSHOT.jar .
EXPOSE 40001
CMD [ "java","-jar","pedidos-0.0.1-SNAPSHOT.jar" ]