# Stage 1: Build the application
FROM maven:3.9.5-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# Stage 2: Deploy to Tomcat
FROM tomcat:10.1.16-jdk17-temurin-jammy
# Remove default webapps to keep it clean
RUN rm -rf /usr/local/tomcat/webapps/*

# Copy the WAR from the build stage to the Tomcat webapps directory
# Renaming to ROOT.war to make it available at http://localhost:8080/
COPY --from=build /app/target/springboot-hello-0.0.1-SNAPSHOT.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080
CMD ["catalina.sh", "run"]
