# cool-app

This is a simple demo app that returns one of several "cool expressions" at random.

This README contains instructions for [building the image](README.md#test-the-app-locally) and [testing the app locally](README.md#build-the-image)

## Build the image

Choose one of the following methods to build an image from the source code:

[Dockerfile](README.md#dockerfile)<br>
[Jib](README.md#jib)<br>
[Spring Boot Maven Plugin](README.md#spring-boot-maven-plugin)<br>
[Pack](README.md#pack)

### Dockerfile

Review the Dockerfile:
```
cat Dockerfile
```

Build the image & publish to the local Docker daemon:
```
./mvnw package
docker build -t cool-app:1.0.0-dockerfile ./
docker images | grep cool
```

Optionally, publish to DockerHub (user your org name instead of the example below):
```
docker login # enter your username and password at the prompts
docker tag cool-app:1.0.0-dockerfile ciberkleid/cool-app:1.0.0-dockerfile
docker push ciberkleid/cool-app:1.0.0-dockerfile
```

### Jib

Review the `profiles` section of the pom file to see the build configuration using `jib-maven-plugin`:
```
cat pom.xml
```

Build the image & publish to the local Docker daemon:
```
./mvnw compile jib:dockerBuild -Pjib -Dimage=cool-app:1.0.0-jib
docker images | grep cool
```

Alternatively, build the image & publish to DockerHub (user your org name instead of the example below):
```
docker login # enter your username and password at the prompts
./mvnw compile jib:build -Pjib -Dimage=ciberkleid/cool-app:1.0.0-jib
```

Read more about Jib and configuring the plugin in your pom file [here](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin#quickstart).

### Spring Boot Maven Plugin

Review the pom file to validate that Spring Boot version is 2.3.0.M1 or greater and that the default build plugin is `spring-boot-maven-plugin`:
```
cat pom.xml
```

Build the image & publish to the local Docker daemon:
```
./mvnw spring-boot:build-image
docker images | grep cool
```

Optionally, publish to DockerHub (user your org name instead of the example below):
```
docker login # enter your username and password at the prompts
docker tag cool-app:1.0.0-boot ciberkleid/cool-app:1.0.0-boot
docker push ciberkleid/cool-app:1.0.0-boot
```

### Pack

Install the [pack CLI](https://buildpacks.io/docs/install-pack/).
 
Build the image & publish to the local Docker daemon:
```
pack build cool-app:1.0.0-pack --builder cloudfoundry/cnb:bionic
docker images | grep cool
```

Alternatively, build the image & publish to DockerHub (user your org name instead of the example below):
```
docker login # enter your username and password at the prompts
pack build ciberkleid/cool-app:1.0.0-pack --builder cloudfoundry/cnb:bionic --publish
```

## Test the app locally

See the image(s) generated by the above approach(es) in your local Docker daemon:
```
docker images | grep cool
```

In one terminal window, start the app. Replace `TAG_SUFFIX` with the appropriate value as shown in the list of images returned by the previous command:
```
container_id=`docker run -d -p 8080:8080 cool-app:1.0.0-TAG_SUFFIX`
```
The `docker run` command returns the id of the running container, which we are storing in a variable. You can also run `docker ps` to see the running containers.

Send a request to the app. You should see a "cool" quote is returned.
```
curl http://localhost:8080; echo
```

Stop the app container using the container_id we captured when starting the app:
```
docker stop $container_id
```