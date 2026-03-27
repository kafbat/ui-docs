# With Docker

## Build & Run

Once you installed the prerequisites and cloned the repository, run the following steps in your project directory:

### Step 1 : Build

* Build a docker image with the app

```bash
./gradlew clean build \  
# -x skips goals, in this cases tests. Tests take time, run them separately if needed.
-x test \
# building an app without frontend part could be useful for developing frontend or for using the app like an API client
-Pinclude-frontend=true \
# skip building a docker image if you only need a jar
-Pbuild-docker-images=true \
# version will be displayed in UI and used for tagging docker image. You can remove it.
-Pversion=<version>
```

A successful build should produce a docker image named `ghcr.io/kafbat/kafka-ui` with whatever version you've supplied.
### Step 2 : Run

**Using Docker Compose**

* Start the app using docker image built in step 1 along with Kafka clusters:
* Edit the `./.dev/dev.yaml` and update `image: ghcr.io/kafbat/kafka-ui:<version>`
  * `<version>` - is the one you just built in the previous step

```bash
docker-compose -f ./.dev/dev.yaml up -d
```

**Using Spring Boot Run**

* If you want to start only kafka clusters (to run the `kafbat-ui` app via `spring-boot:run`):

```bash
docker-compose -f ./documentation/compose/kafka-clusters-only.yaml up -d
```

* Then start the app.

```bash
./gradlew bootRun -x test

# or

./gradlew bootRun -x test -Dspring.config.location=file:///path/to/conf.yaml
```

**Running in kubernetes**

* Using Helm Charts

```bash
helm repo add kafbat https://ui.charts.kafbat.io
helm install kafbat-ui kafbat/kafka-ui
```

To read more please follow to chart documentation.

### Step 3 : Access Kafbat-UI

* To see the app running, navigate to http://localhost:8080.
