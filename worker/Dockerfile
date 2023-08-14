FROM maven:3.6.1-jdk-8-slim
WORKDIR /app
COPY . .
RUN mvn package
RUN mv target/worker-jar-with-dependencies.jar /run/worker.jar
RUN rm -rf /app/*
CMD java -jar /run/worker.jar
