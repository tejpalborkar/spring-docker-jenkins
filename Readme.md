## Spring Boot WebSocket Chat Appplication

You can checkout the live version of the application at https://spring-ws-chat.herokuapp.com/

![App Screenshot](screenshot.png)

## Requirements

1. Java - 1.8.x

2. Maven - 3.x.x

## Steps to Setup

**1. Clone the application**

```bash
git clone https://github.com/callicoder/spring-boot-websocket-chat-demo.git
```

**2. Build and run the app using maven**

```bash
cd spring-boot-websocket-chat-demo
mvn package
java -jar target/websocket-demo-0.0.1-SNAPSHOT.jar
```

Alternatively, you can run the app directly without packaging it like so -

```bash
mvn spring-boot:run
```

## Learn More

You can find the tutorial for this application on my blog -

https://www.callicoder.com/spring-boot-websocket-chat-example/

## Docker

## 1.  Build Docker imaage
docker build --no-cache -t spring-docker-jenkins .

## 2.  Tag docker image
docker tag spring-docker-jenkins:latest tejpalborkar10/spring-docker-jenkins:latest

## 3. Push image to docker hub
docker push tejpalborkar10/spring-docker-jenkins:latest

## 4. Below command will run the docker container on port 9091
docker run --rm -d -p 9091:8080 --name spring-docker-jenkins -t -d tejpalborkar10/spring-docker-jenkins:latest


