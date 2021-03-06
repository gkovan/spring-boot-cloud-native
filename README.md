# spring-boot-cloud-native

Number of ways to create a spring boot container image.

## Summary

| Method | Layers | Size | Dockerfile |
| ------ | ------ | ---- | ---------- |
| Buildpack | 15 | 261 MB | NA |
| Jib | 8 | 227 MB | NA |
| Dockerfile - jdk - fat jar | 4 | 455 MB | rest-server-dockerfile:0.0.1 |
| Dockerfile - jre - fat jar | 4 | 262 MB | rest-server-dockerfile-jre:0.0.1 |
| Dockerfile - jre - multistage | 8 | 262 MB | rest-server-dockerfile-multistage-layered |
| Dockerfile - jdk - ubi | 4 | 455 MB | rest-server-dockerfile-ubi-openjdk11 |



## Buildpaks

Spring Boot 2.3.0.M1 and above includes buildpack support directly for both Maven and Gradle.
Using the buildpack approach, a Dockerfile does not need to be created.  The buildpack provides the Java runtime for the application.

Build docker image:
```
./mvnw spring-boot:build-image
```
The name of the published image will be your application name and the tag will be the version.

Run the image:
```
docker run -it -p8080:8080 rest-service:0.0.1-SNAPSHOT
```

Test the image:
```
curl http://localhost:8080/greeting
```

Inspect the image:
```
dive -j rest-service-buildpack-dive.txt rest-service:0.0.1-SNAPSHOT
```

Size of image: 261MB

Sources:
* https://spring.io/blog/2020/01/27/creating-docker-images-with-spring-boot-2-3-0-m1
* https://spring.io/guides/topicals/spring-boot-docker/
* https://www.baeldung.com/docker-layers-spring-boot

## Jib

Jib builds optimized Docker and OCI images for Java applications with a Docker Daemon.


To use JIB, you need to add a maven plugin:
```
<plugin>
    <groupId>com.google.cloud.tools</groupId>
    <artifactId>jib-maven-plugin</artifactId>
    <version>2.6.0</version>
</plugin>
```


Build docker image:
```
./mvnw compile jib:dockerBuild -Dimage=rest-server-jib:0.0.1
```

Run the image:
```
docker run -it -p8080:8080 rest-server-jib:0.0.1
```

Test the image:
```
curl http://localhost:8080/greeting
```

Size of image: 227MB

Source:
* https://github.com/GoogleContainerTools/jib
* https://cloud.google.com/blog/products/application-development/introducing-jib-build-java-docker-images-better


## Dockerfile - one stage

This is the traditional approach where a fat jar is built using `mvn package` that contains all the java artifacts such as application code, spring boot libraries and any dependencies. A Dockerfile needs to be created.

Build docker image:
```
docker build -t rest-server-dockerfile:0.0.1 .
```

Run docker image:
```
docker run -it -p8080:8080 rest-server-dockerfile:0.0.1
```

Test the image:
```
curl http://localhost:8080/greeting
```

Inspect the image:
```
dive -j rest-server-dockerfile.dive rest-server-dockerfile:0.0.1
```
Size of image: 455MB

## Dockerfile - jre

This is the traditional approach where a fat jar is built using `mvn package` that contains all the java artifacts such as application code, spring boot libraries and any dependencies. A Dockerfile needs to be created.

Build docker image:
```
docker build -f Dockerfile.jre -t rest-server-dockerfile-jre:0.0.1 .
```

Run docker image:
```
docker run -it -p8080:8080 rest-server-dockerfile-jre:0.0.1
```

Test the image:
```
curl http://localhost:8080/greeting
```

Inspect the image:
```
dive -j rest-server-dockerfile-jre.dive rest-server-dockerfile-jre:0.0.1
```
Size of image: 262MB

## Dockerfile - multistage layered

To list the java layers to include in the Dockerfille.multistage-layered file run this command:
```
java -Djarmode=layertools -jar target/rest-service-0.0.1-SNAPSHOT.jar list
```

Build docker image:
```
docker build -f Dockerfile.multistage-layered -t rest-server-dockerfile-multistage-layered:0.0.1 .
```

Run docker image:
```
docker run -it -p8080:8080 rest-server-dockerfile-multistage-layered:0.0.1
```

Inspect the image:
```
dive -j rest-server-dockerfile-multistage-layered.dive rest-server-dockerfile-multistage-layered:0.0.1
```

Size of image: 262MB

## Dockerfile ubi image - one stage

Build docker image:
```
docker build -f Dockerfile.ubi-openjdk11 -t rest-server-dockerfile-ubi-openjdk11:0.0.1 .
```

Run docker image:
```
docker run -it -p8080:8080 rest-server-dockerfile-ubi-openjdk11:0.0.1
```

Inspect the image:
```
dive -j rest-server-dockerfile-ubi-openjdk11.dive rest-server-dockerfile-ubi-openjdk11:0.0.1
```

Size of image: 455MB
