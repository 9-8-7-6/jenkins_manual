# jenkins_manual
Simple way to build jenkins step by step!

Suggest you to install Jenkins with docker.

1. **Install Docker first**  
   (This step is assumed and will be skipped.)

2. Create a bridge network in Docker using the following `docker network create` command:
   ```bash
   docker network create jenkins

3. To allow Jenkins to execute Docker commands, download and run the docker:dind Docker image with the following docker run command:

    ```bash
    docker run --name jenkins-docker --rm --detach \
    --privileged --network jenkins --network-alias docker \
    --env DOCKER_TLS_CERTDIR=/certs \
    --volume jenkins-docker-certs:/certs/client \
    --volume jenkins-data:/var/jenkins_home \
    --publish 2376:2376 \
    docker:dind --storage-driver overlay2

4. Create the Dockerfile with the following content (feel free to customize it as needed):

    ```Dockerfile
    FROM jenkins/jenkins:2.462.3-jdk17
    USER root
    RUN apt-get update && apt-get install -y lsb-release
    RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
    https://download.docker.com/linux/debian/gpg
    RUN echo "deb [arch=$(dpkg --print-architecture) \
    signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
    https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
    RUN apt-get update && apt-get install -y docker-ce-cli
    USER jenkins
    RUN jenkins-plugin-cli --plugins "blueocean docker-workflow"

5. Build a new Docker image from this Dockerfile and name it myjenkins-blueocean:2.462.3-1 using the following command:

    ```bash
    docker build -t myjenkins-blueocean:2.462.3-1 .

6. Run your own myjenkins-blueocean:2.462.3-1 image as a container in Docker using the following docker run command:

    ```bash
    docker run --name jenkins-blueocean --restart=on-failure --detach \
    --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
    --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
    --publish 8080:8080 --publish 50000:50000 \
    --volume jenkins-data:/var/jenkins_home \
    --volume jenkins-docker-certs:/certs/client:ro \
    myjenkins-blueocean:2.462.3-1

7. By default,the Jenkins url is http://127.0.0.1:8080/, once you enter the website,you need the password for unlocking Jenkins,paste the command in terminal and copy the the website:

   ```bash
    docker exec jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword

8. Select "Install suggested plugins"
9. browse the website and register a account for yourself
10. Start to practice Jenkins!!