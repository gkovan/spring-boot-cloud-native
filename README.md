# spring-boot-cloud-native

Number of ways to create a spring boot container image.

## Buildpaks

Spring Boot 2.3.0.M1 and above includes buildpack support directly for both Maven and Gradle.

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


## Dockerfile - one stage

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
