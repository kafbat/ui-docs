---
description: Build & Run Without Docker
---

# Without Docker

Once you installed the prerequisites and cloned the repository, run the following steps in your project directory:

### Running Without Docker Quickly <a href="#run_without_docker_quickly" id="run_without_docker_quickly"></a>

* [Download the latest kafbat-ui jar file](https://github.com/kafbat/kafka-ui/releases)

**Execute the jar**

```bash
java -Dspring.config.additional-location=<path-to-application-local.yml> --add-opens java.rmi/javax.rmi.ssl=ALL-UNNAMED -jar <path-to-kafbat-ui-jar>
```

* Example of how to configure clusters in the [application-local.yml](https://github.com/kafbat/kafka-ui/blob/main/api/src/main/resources/application-local.yml) configuration file.

### Building And Running Without Docker <a href="#build_and_run_without_docker" id="build_and_run_without_docker"></a>

> _**NOTE:**_ If you want to get kafbat-ui up and running locally quickly without building the jar file manually, then just follow Running Without Docker Quickly

Proceed with building instructions from [[with-docker]] and disable/remove `build-docker-images` flag.

* Command to build the jar

> Once your build is successful and the jar file named kafbat-ui-api-0.0.1-SNAPSHOT.jar is generated inside `kafbat-ui-api/target`.

* Execute the jar
