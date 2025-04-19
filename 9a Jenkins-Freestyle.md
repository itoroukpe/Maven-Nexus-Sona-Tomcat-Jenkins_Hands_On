Here's a **step-by-step guide** to set up a **Jenkins Freestyle project** using the GitHub repo `https://github.com/itoroukpe/maven-web-application.git` and integrate it with **SonarQube**, **Nexus**, and **Tomcat**:

---

## ✅ Prerequisites

Before setting up the Jenkins job, ensure you have the following:

### 1. **Jenkins Server** with:
- Git plugin
- Maven plugin
- SonarQube Scanner plugin
- Nexus Artifact Uploader plugin (or `mvn deploy`)
- Deploy to container plugin (for Tomcat)
- Credentials plugin

### 2. **External Tools Installed**:
- **SonarQube server** up and running (e.g., `http://localhost:9000`)
- **Nexus Repository Manager** configured (e.g., `http://your-nexus:8081`)
- **Tomcat Server** with manager app enabled (e.g., `http://your-tomcat:8080`)

---

## 🛠️ Step-by-Step Setup

---

### **Step 1: Configure Tools in Jenkins Global Configuration**

#### 🔧 1.1 Maven
- Go to: `Manage Jenkins` > `Global Tool Configuration`
- Under **Maven**, add:
  - Name: `Maven 3.8.1`
  - Install automatically or point to your local Maven installation

#### 🔧 1.2 JDK
- Under **JDK**, add your Java version (e.g., JDK 11 or 17)

#### 🔧 1.3 Git
- Ensure Git is installed or defined here

#### 🔧 1.4 SonarQube
- Go to: `Manage Jenkins` > `Configure System`
- Under **SonarQube servers**:
  - Name: `SonarQube`
  - Server URL: `http://localhost:9000`
  - Add **Authentication Token**

#### 🔧 1.5 Nexus Credentials
- Go to: `Manage Jenkins` > `Manage Credentials`
- Add credentials:
  - Type: Username with password
  - ID: `nexus-credentials`

#### 🔧 1.6 Tomcat Manager Credentials
- Add:
  - Type: Username with password
  - ID: `tomcat-credentials`

---

### **Step 2: Create Freestyle Project**

#### 🎯 2.1 Create Job
- Go to Jenkins dashboard → `New Item`
- Name: `Maven-WebApp-Freestyle`
- Select **Freestyle project**
- Click **OK**

---

### **Step 3: Configure the Freestyle Project**

#### 🔗 3.1 Source Code Management
- Choose **Git**
  - Repo URL: `https://github.com/itoroukpe/maven-web-application.git`
  - Branch: `*/main` (or whichever you use)

#### ⚙️ 3.2 Build Environment
- Check **Delete workspace before build starts** (optional for clean builds)

#### 🏗️ 3.3 Build Triggers (Optional)
- Set `Poll SCM` or `GitHub hook trigger` if desired

---

### **Step 4: Add Build Steps**

---

#### 🔍 **4.1 SonarQube Analysis**
- **Add build step** → Execute SonarQube Scanner
- Set **Analysis properties**:
```properties
sonar.projectKey=maven-web-application
sonar.projectName=Maven Web Application
sonar.projectVersion=1.0
sonar.sources=src
sonar.java.binaries=target/classes
sonar.host.url=http://localhost:9000
sonar.login=<your-sonarqube-token>
```

---

#### 🧱 **4.2 Maven Build**
- **Add build step** → Invoke top-level Maven targets
- Goals: `clean install`
- Maven version: Use the one configured globally

---

#### 📦 **4.3 Deploy to Nexus**
##### Option A: If using `mvn deploy`:
- Add a **profile** in your `pom.xml` to define `<distributionManagement>` for Nexus
- Add credentials via `settings.xml` or use Jenkins credential injection
- Replace build goals:
```bash
clean deploy -Prelease
```

##### Option B: Use **"Upload Artifact to Nexus"** build step (plugin):
- **Add post-build action** → Upload artifact to Nexus
- Group ID, Artifact ID, Version, Artifact Path (e.g., `target/your-app.war`)

---

#### 🚀 **4.4 Deploy to Tomcat**
- **Add post-build action** → Deploy war/ear to a container
- WAR/EAR files: `target/*.war`
- Context path: `/maven-web-application`
- Container:
  - **Tomcat 7+**
  - URL: `http://your-tomcat-host:8080`
  - Credentials: `tomcat-credentials`

---

### ✅ Final Config Summary

| Task                  | Tool       | Jenkins Configuration                         |
|-----------------------|------------|-----------------------------------------------|
| Source Control        | GitHub     | Git plugin with repo URL                      |
| Build                 | Maven      | `clean install` or `clean deploy`             |
| Code Quality          | SonarQube  | Sonar Scanner plugin with `sonar.*` props     |
| Artifact Deployment   | Nexus      | mvn deploy or Nexus Upload plugin             |
| App Deployment        | Tomcat     | Deploy WAR post-build plugin                  |

---

### ✅ Build and Test

- Click **Save** and **Build Now**
- Monitor console output for each stage:
  - Cloning the repo
  - SonarQube analysis
  - Build success
  - Upload to Nexus
  - Deployment to Tomcat

---

Here’s a **sample `pom.xml`** that includes:

- **Maven build configuration**
- **SonarQube plugin**
- **Nexus deployment (`distributionManagement`)**
- **Profiles for build environments**

---

### ✅ Sample `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.itoroukpe</groupId>
  <artifactId>maven-web-application</artifactId>
  <version>1.0.0</version>
  <packaging>war</packaging>

  <name>Maven Web Application</name>

  <properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <!-- Example Servlet API dependency -->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <scope>provided</scope>
    </dependency>
    <!-- Add other dependencies as needed -->
  </dependencies>

  <!-- SonarQube Plugin Configuration -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
      </plugin>

      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>sonar-maven-plugin</artifactId>
        <version>3.9.1.2184</version>
      </plugin>
    </plugins>
  </build>

  <!-- Nexus Repository Configuration -->
  <distributionManagement>
    <repository>
      <id>nexus-releases</id>
      <name>Nexus Release Repository</name>
      <url>http://<your-nexus-host>:8081/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
      <id>nexus-snapshots</id>
      <name>Nexus Snapshot Repository</name>
      <url>http://<your-nexus-host>:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
  </distributionManagement>

  <!-- Optional: Profiles for dev/test/prod -->
  <profiles>
    <profile>
      <id>release</id>
      <activation>
        <property>
          <name>env</name>
          <value>prod</value>
        </property>
      </activation>
      <properties>
        <!-- Custom properties for release -->
      </properties>
    </profile>
  </profiles>
</project>
```

---

### 🔐 Maven Settings: `settings.xml`

To inject credentials for Nexus securely, add this to your Maven `settings.xml` (usually in `~/.m2/settings.xml`):

```xml
<servers>
  <server>
    <id>nexus-releases</id>
    <username>your-nexus-username</username>
    <password>your-nexus-password</password>
  </server>
  <server>
    <id>nexus-snapshots</id>
    <username>your-nexus-username</username>
    <password>your-nexus-password</password>
  </server>
</servers>
```

---

