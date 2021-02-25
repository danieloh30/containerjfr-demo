# containerjfr-demo project

This project uses Quarkus, the Supersonic Subatomic Java Framework.

If you want to learn more about Quarkus, please visit its website: https://quarkus.io/ .

## Running the application in dev mode

You can run your application in dev mode that enables live coding using:
```shell script
./mvnw compile quarkus:dev
```

> **_NOTE:_**  Quarkus now ships with a Dev UI, which is available in dev mode only at http://localhost:8080/q/dev/.

## Packaging and running the application

The application can be packaged using:
```shell script
./mvnw package
```
It produces the `quarkus-run.jar` file in the `target/quarkus-app/` directory.
Be aware that it’s not an _über-jar_ as the dependencies are copied into the `target/quarkus-app/lib/` directory.

If you want to build an _über-jar_, execute the following command:
```shell script
./mvnw package -Dquarkus.package.type=uber-jar
```

The application is now runnable using `java -jar target/quarkus-app/quarkus-run.jar`.

## Creating a native executable

You can create a native executable using: 
```shell script
./mvnw package -Pnative
```

Or, if you don't have GraalVM installed, you can run the native executable build in a container using: 
```shell script
./mvnw package -Pnative -Dquarkus.native.container-build=true
```

You can then execute your native executable with: `./target/getting-started-1.0.0-SNAPSHOT-runner`

If you want to learn more about building native executables, please consult https://quarkus.io/guides/maven-tooling.html.

## Provided examples

### RESTEasy JAX-RS example

REST is easy peasy with this Hello World RESTEasy resource.

[Related guide section...](https://quarkus.io/guides/getting-started#the-jax-rs-resources)

## Deploy Vert.x App

```
oc new-app quay.io/andrewazores/vertx-fib-demo:0.1.0
```

## Deploy JMX Listener

```
oc new-app quay.io/andrewazores/container-jmx-docker-listener:0.1.0 --name=jmx-listener
oc patch svc/jmx-listener -p '{"spec":{"$setElementOrder/ports":[{"port":7095},{"port":9092},{"port":9093}],"ports":[{"name":"jfr-jmx","port":9093}]}}'
```

## Package and Deploy Quarkus App

```
mvn package

oc new-build registry.access.redhat.com/ubi8/openjdk-11 --binary --name=quarkus-jvm -l app=quarkus-jvm

oc start-build quarkus-jvm --from-dir target/quarkus-app/ --follow

oc new-app quarkus-jvm -e JAVA_OPTIONS='-Dcom.sun.management.jmxremote.port=9091 -Dcom.sun.management.jmxremote.rmi.port=9091 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.registry.ssl=false'

oc label deployment/quarkus-jvm app.openshift.io/runtime=quarkus --overwrite
```

## Add jfr-jmx service to Quarkus

```
    - name: jfr-jmx
      protocol: TCP
      port: 9091
      targetPort: 9091
```
