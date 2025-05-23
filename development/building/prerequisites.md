# Prerequisites

### Prerequisites

This page explains how to get the software you need to use on Linux or macOS for local development.

* `java 21` package or newer
* `git` installed
* `docker` installed

> Note: For contributing, you must have a github account to be able to raise PRs.

#### For Linux

1. Install `OpenJDK 21` package or newer:

```bash
sudo apt update
sudo apt install openjdk-21-jdk
```

* Check the java version using the command `java -version`.

```bash
openjdk version "21.0.5" 2024-10-15
OpenJDK Runtime Environment (build 21.0.5+8-Ubuntu-2ubuntu120.04)
OpenJDK 64-Bit Server VM (build 21.0.5+8-Ubuntu-2ubuntu120.04, mixed mode, sharing)
```

Note: In case OpenJDK 21 is not set as your default Java, run

```bash
sudo update-alternatives --config java
```

to list all installed Java versions.

```
Selection    Path                                            Priority   Status
------------------------------------------------------------
* 0            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      auto mode
  1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      manual mode
  2            /usr/lib/jvm/java-16-openjdk-amd64/bin/java      1051      manual mode
  3            /usr/lib/jvm/java-17-openjdk-amd64/bin/java      1021      manual mode
  4            /usr/lib/jvm/java-21-openjdk-amd64/bin/java      1001      manual mode

Press <enter> to keep the current choice[*], or type selection number:
```

You can set it as the default by entering the selection number for it in the list and pressing Enter. For example, to set Java 21 as the default, you would enter "4" and press **Enter**.

2. Install `git`:

```bash
sudo apt install git
```

3. Install `docker`:

```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository -y "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
apt-cache policy docker-ce
sudo apt -y install docker-ce
```

To execute the `docker` Command without `sudo`:

```bash
sudo usermod -aG docker ${USER}
su - ${USER}
sudo chmod 666 /var/run/docker.sock
```

#### For macOS

1. Install [brew](https://brew.sh/).
2. Install brew cask:

```bash
brew cask
```

3. Install openjdk 21 via Homebrew:

```bash
brew install openjdk@21
```

4. Verify Installation

```bash
java -version
```

Note: In case OpenJDK 21 is not set as your default Java, you can consider including it in your `$PATH` after installation

```bash
export PATH="$(/usr/libexec/java_home -v 21)/bin:$PATH"
export JAVA_HOME="$(/usr/libexec/java_home -v 21)"
```

If java\_home doesn't recognize homebrew installed java you can run below cmd to symlink brew installed java path to jvm

```bash
sudo ln -sfn $(brew --prefix openjdk@21)/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-21.jdk
```

### Tips

Consider allocating not less than 4GB of memory for your docker. Otherwise, some apps within a stack (e.g. `kafbat-ui.yaml`) might crash.

To check how much memory is allocated to docker, use `docker info`.

You will find the total memory and used memory in the output. if you won't find used memory that means memory limits are not set for containers.

**To allocate 4GB of memory for Docker:**

**MacOS**

Edit docker daemon settings within docker dashboard

**For Ubuntu**

1. Open the Docker configuration file in a text editor using the following command:

```bash
sudo nano /etc/default/docker
```

2. Add the following line to the file to allocate 4GB of memory to Docker:

```properties
DOCKER_OPTS="--default-ulimit memlock=-1:-1 --memory=4g --memory-swap=-1"
```

3. Save the file and exit the text editor.
4. Restart the Docker service using the following command:

```bash
sudo service docker restart
```

5. Verify that the memory limit has been set correctly by running the following command:

```bash
docker info | grep -i memory
```

Note that the warning messages are expected as they relate to the kernel not supporting cgroup memory limits.

Now any containers you run in docker will be limited to this amount of memory. You can also increase the memory limit as per your preference.

### Where to go next

In the next section, you'll learn how to Build and Run kafbat-ui.
