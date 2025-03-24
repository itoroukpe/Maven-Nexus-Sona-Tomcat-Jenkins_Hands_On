Here's a practical, step-by-step hands-on guide to teach your students how to containerize a Java application using **Docker**, assuming the application artifact (**WAR file**) is stored in a **Nexus Repository**.

---

# 🐳 **Step-by-Step Hands-On: Containerize a Java Application from Nexus with Docker**

---

## 🎯 **Objective**

After this practical, students will:

- Understand Docker basics.
- Pull artifacts (WAR files) from Nexus repository.
- Create a Dockerfile to containerize a Java application.
- Build, run, and test Docker containers locally.

---

## ✅ **Prerequisites**

Make sure you have installed:

- **Docker Desktop** ([Docker install](https://docs.docker.com/get-docker/))
- **Java Application** WAR file deployed in Nexus Repository
- Nexus running (`http://localhost:8081`)
- Apache Tomcat (Docker will handle this)
- Basic familiarity with CLI/terminal

Verify Docker is installed properly:

```bash
docker --version
docker run hello-world
```

---

## 🚀 **Step-by-Step Practical Guide**

---

## 🟢 **Step 1: Verify WAR File in Nexus**

- Open Nexus (`http://localhost:8081`).
- Ensure your WAR artifact exists, for example:
```
com/example/myapp/1.0.0/myapp-1.0.0.war
```

---

## 🟢 **Step 2: Create Project Directory**

Create a folder named `docker-java-app`:

```bash
mkdir docker-java-app
cd docker-java-app
```

---

## 🟢 **Step 3: Download WAR Artifact from Nexus**

Option 1: **Manual Download**  
- Download WAR from Nexus UI directly into the `docker-java-app` folder and rename it:
  ```
  app.war
  ```

Option 2: **Using curl** (recommended)

```bash
curl -u admin:admin123 \
  -O "http://localhost:8081/repository/maven-releases/com/example/myapp/1.0.0/myapp-1.0.0.war"

# rename for simplicity
mv myapp-1.0.0.war app.war
```

---

## 🟢 **Step 4: Create Dockerfile**

Inside `docker-java-app` folder, create a file named `Dockerfile`:

```dockerfile
# Use official Tomcat Docker image
FROM tomcat:10.1-jdk11-openjdk

# Remove default webapps
RUN rm -rf /usr/local/tomcat/webapps/*

# Copy WAR file into Tomcat webapps folder
COPY app.war /usr/local/tomcat/webapps/ROOT.war

# Expose default Tomcat port
EXPOSE 8080

# Start Tomcat server
CMD ["catalina.sh", "run"]
```

**Explanation:**

- Starts from official Tomcat base image.
- Removes default Tomcat apps.
- Copies your WAR file into Tomcat.
- Exposes port `8080`.

---

## 🟢 **Step 5: Build Docker Image**

Run this command inside your project folder (`docker-java-app`):

```bash
docker build -t java-nexus-app:1.0 .
```

- `-t`: tags the image (name:version)
- `.`: the current directory context (where Dockerfile is located)

Check the built image:

```bash
docker images
```

---

## 🟢 **Step 6: Run the Docker Container**

Run a container from your image:

```bash
docker run -d -p 9090:8080 --name my-java-app java-nexus-app:1.0
```

- `-d`: Run container in background.
- `-p 9090:8080`: Map container's port 8080 to your host's port 9090.
- `--name`: Assign a friendly container name.

Check the running container:

```bash
docker ps
```

---

## 🟢 **Step 7: Test Your Application**

- Open a browser and access your application at:
```
http://localhost:9090/
```

Your Java application running inside Docker via Tomcat should appear.

---

## 🟢 **Step 8: Container Management**

Useful Docker commands for students:

| Command                             | Description                      |
|-------------------------------------|----------------------------------|
| `docker ps`                         | List running containers          |
| `docker logs my-java-app`           | View container logs              |
| `docker stop my-java-app`           | Stop the container               |
| `docker start my-java-app`          | Restart stopped container        |
| `docker rm my-java-app`             | Remove container                 |
| `docker rmi java-nexus-app:1.0`     | Remove image                     |

---

## 🟢 **Step 9: (Optional) Push Docker Image to Docker Hub**

If students have Docker Hub accounts:

1. **Log in**:

```bash
docker login
```

2. **Tag image**:

```bash
docker tag java-nexus-app:1.0 yourdockerhubusername/java-nexus-app:1.0
```

3. **Push image**:

```bash
docker push yourdockerhubusername/java-nexus-app:1.0
```

---

## 🚨 **Common Troubleshooting**

- **Container not starting**:
  ```bash
  docker logs my-java-app
  ```
- **Port already in use**:
  - Change port mapping, e.g. `-p 9091:8080`.

- **Nexus download issues**:
  - Verify credentials (`curl -u username:password`).

---

## 📚 **Learning Outcomes**

Upon completion of this hands-on practical, students will:

- Understand how Docker containers work.
- Gain skills in containerizing Java apps from artifact repositories.
- Develop practical skills in Dockerfile creation and container management.
- Be prepared for real-world Docker scenarios.

This practical equips students with foundational Docker skills, essential for modern software development and deployment.
