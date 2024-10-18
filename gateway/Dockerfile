FROM maven:3.9.9-eclipse-temurin-17-focal AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre-focal
WORKDIR /app
COPY --from=build /app/target/gateway-0.0.1-SNAPSHOT.jar .
EXPOSE 8082
CMD [ "java","-jar","gateway-0.0.1-SNAPSHOT.jar" ]