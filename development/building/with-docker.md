# With Docker

## Build & Run

Once you installed the prerequisites and cloned the repository, run the following steps in your project directory:

### Step 1 : Build

> Skip the maven tests as they might not be successful

* Build a docker image with the app

```
./mvnw clean install -Pprod
```

* if you need to build the frontend module, go to
  * frontend-build-documentation
* In case you want to build `api` module by skipping the tests

```
./mvnw clean install -Dmaven.test.skip=true -Pprod
```

* To build only the `api` module you can use this command:

```
./mvnw -f api/pom.xml clean install -Pprod -DskipUIBuild=true
```

If this step is successful, it should create a docker image named `ghcr.io/kafbat/kafka-ui` with `latest` tag on your local machine except macOS M1.

### Step 2 : Run

**Using Docker Compose**

* Start the app using docker image built in step 1 along with Kafka clusters:

```
docker-compose -f ./documentation/compose/kafbat-ui.yaml up -d
```

**Using Spring Boot Run**

* If you want to start only kafka clusters (to run the `kafbat-ui` app via `spring-boot:run`):

```
docker-compose -f ./documentation/compose/kafka-clusters-only.yaml up -d
```

* Then start the app.

```
./mvnw spring-boot:run -Pprod

# or

./mvnw spring-boot:run -Pprod -Dspring.config.location=file:///path/to/conf.yaml
```

**Running in kubernetes**

* Using Helm Charts

```
helm repo add kafbat-ui https://kafbat.github.io/helm-charts
helm install kafka-ui kafka-ui/kafka-ui
```

To read more please follow to chart documentation.

### Step 3 : Access Kafbat-UI

* To see the app running, navigate to http://localhost:8080.
