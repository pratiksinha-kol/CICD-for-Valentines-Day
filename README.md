
# Valentine's Day CI/CD Project  

[Watch the Video](https://youtu.be/_Xo3eYAz7Zo?list=PLAdTNzDIZj_9c-o43WJ7yYBa5YEAkWiOv)

### Prerequisites

Ensure the following are installed on the main server or within Docker containers:
- **Java**
- **Jenkins**
- **Docker**

In this guide, Jenkins and SonarQube are run locally as Docker containers.

### Steps:

#### 1. Set Up Jenkins with Docker
Run the following command to start Jenkins with Docker:

```bash
docker run -d --name jenkins-dind -p 8080:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):/usr/bin/docker -u root -e DOCKER_GID=$(getent group docker | cut -d: -f3) jenkins/jenkins:lts
```

#### 2. Configure Docker Inside Jenkins
Access the Jenkins container using the following command:

```bash
docker exec -it jenkins-dind bash
```

Then, run these commands to ensure Jenkins can use Docker:

```bash
groupadd -for -g $DOCKER_GID docker
usermod -aG docker jenkins
```

#### 3. Install and Configure SonarQube
Find the official SonarQube image on Docker Hub and ensure you meet the [Docker Host Requirements](https://hub.docker.com/_/sonarqube). Then run these commands:

```bash
sysctl -w vm.max_map_count=524288
sysctl -w fs.file-max=131072
ulimit -n 131072
ulimit -u 8192
```

Now, start the SonarQube container:

```bash
docker run -d --name sonarqube-dind -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true -p 9000:9000 sonarqube:latest
```

The default login credentials for SonarQube are **admin/admin**. Change the password to something more secure, like `Sonarqube@123`.

#### 4. Install Jenkins Plugins
In Jenkins, install the following plugins:
- **Docker**
- **Docker Pipeline**
- **Docker API**
- **Docker Build Steps**
- **SonarQube Scanner**
- **Sonar Quality Gates**

#### 5. Generate SonarQube Token
Generate a token in SonarQube for Jenkins to use. Example token:

```
squ_f2f2b49a1bf0c4a1e7e971987a063e309662f1b5
```

#### 6. Install Trivy for Security Scanning
Install Trivy on the Jenkins server by running:

```bash
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin v0.18.3
```

#### 7. Configure Jenkins Tools
In Jenkins, go to **Manage Jenkins > Tools** and configure:
- **SonarQube Scanner**
- **Docker**

#### 8. Create a Jenkins Pipeline
When creating a new pipeline, select the type as **Pipeline**.

#### 9. Networking for Jenkins and SonarQube
If running Jenkins and SonarQube locally via Docker, set the SonarQube server to:

```
http://sonarqube-dind:9000
```

Create a Docker network and connect the Jenkins and SonarQube containers:

```bash
docker network create dind-network
docker network connect dind-network jenkins-dind
docker network connect dind-network sonarqube-dind
```

To verify they are connected, log in to the Jenkins container and ping the SonarQube server:

```bash
apt-get update
apt install iputils-ping
docker exec -it jenkins-dind ping sonarqube-dind
```

#### 10. Set Up Jenkins Credentials
In Jenkins, create credentials for Docker, Git, and SonarQube.

#### 11. Configure Tools
Ensure that SonarQube and Docker are properly configured under **Tools** in Jenkins.

#### 12. Build the Pipeline
Build and test the pipeline.

#### 13. Verify the Application
Confirm that the Docker application is running by visiting:

```
http://localhost:8082/yes.html
```
