Here's a detailed, practical, step-by-step hands-on guide specifically focused on **Installing, Configuring, and Using Essential Jenkins Plugins**:

---

# 🛠 **Practical Hands-On: Jenkins Plugins Setup & Configuration**

In this session, students will learn how to:

- Identify essential Jenkins plugins.
- Install Jenkins plugins correctly.
- Configure plugins for effective CI/CD workflows.
- Utilize installed plugins within Jenkins jobs.

---

## ✅ **Prerequisites**

Ensure you have:

- Jenkins installed & running (e.g., at `http://localhost:8080`)
- Admin-level access to Jenkins
- Git & Maven installed on your machine
- A GitHub repository with a Java/Maven project (for testing)

---

## 🎯 **Plugins Covered**

Students will install, configure, and use these key plugins:

1. **Git plugin**
2. **Maven Integration plugin**
3. **SonarQube Scanner plugin**
4. **Nexus Artifact Uploader plugin**
5. **Deploy to Container plugin (Tomcat)**

---

## 🚀 **Step-by-Step Guide**

---

## ▶️ **Step 1: Access Jenkins Plugin Manager**

- Log into Jenkins at:
```
http://localhost:8080
```

- Go to **Dashboard → Manage Jenkins → Manage Plugins**.

---

## ▶️ **Step 2: Install Essential Plugins**

- Select the **Available** tab.
- Search and select the following plugins (if not already installed):

  - ✅ **Git Plugin**
  - ✅ **Maven Integration Plugin**
  - ✅ **SonarQube Scanner**
  - ✅ **Nexus Artifact Uploader**
  - ✅ **Deploy to Container (Tomcat)**

- Click **Install without restart** or **Install and restart Jenkins** if prompted.

---

## ▶️ **Step 3: Verify Plugins Installation**

- Go to **Manage Jenkins → Manage Plugins → Installed**.
- Ensure all plugins are successfully installed and active (no warnings).

---

## ▶️ **Step 4: Configure Git Plugin**

- Navigate to **Manage Jenkins → Global Tool Configuration**.
- Under **Git**, ensure Git installation is recognized (usually default).

Example:
- Name: `Default`
- Path: `/usr/bin/git` (Linux/Mac) or `C:\Program Files\Git\bin\git.exe` (Windows)

---

## ▶️ **Step 5: Configure Maven Plugin**

- **Manage Jenkins → Global Tool Configuration → Maven**:
  - Click **Add Maven**
    - Name: `maven3`
    - Check **Install Automatically**
    - Select Maven version (e.g., `3.9.6`).

---

## ▶️ **Step 6: Configure SonarQube Scanner Plugin**

- Go to **Manage Jenkins → Configure System → SonarQube Servers**:
  - Click **Add SonarQube**
    - Name: `LocalSonarQube`
    - Server URL: `http://localhost:9000`
    - Server authentication token:
      - Generate from SonarQube (`My Account → Security → Generate Token`).

- Save settings.

---

## ▶️ **Step 7: Configure Nexus Artifact Uploader Plugin**

- **Manage Jenkins → Configure System → Nexus**:
  - Nexus URL: `http://localhost:8081`
  - Credentials:
    - Add new credentials (`admin/admin123`)

- Save settings.

---

## ▶️ **Step 8: Configure Deploy to Container Plugin (Tomcat)**

- **Manage Jenkins → Configure System → Deploy to Container**:
  - Click **Add Container → Tomcat 9.x/10.x**
  - Tomcat URL: `http://localhost:8080`
  - Credentials (Tomcat manager username/password):
    - Example: `tomcat/tomcat` or create your own from Tomcat's `tomcat-users.xml`.

- Save settings.

---

## ▶️ **Step 9: Create a Jenkins Job to Test All Plugins**

- Jenkins dashboard → **New Item**
  - Name: `JenkinsPluginDemo`
  - Choose **Freestyle project** and click **OK**.

Configure your job with:

### ⚙️ **A) Source Code Management (Git)**

- Select **Git**
- Repository URL: (Your GitHub Repo URL)
- Credentials: (If your repo is private, configure GitHub credentials)

### ⚙️ **B) Build Steps (Maven Integration)**

- Add build step: **Invoke top-level Maven targets**
  - Maven version: `maven3`
  - Goals: `clean package`

### ⚙️ **C) SonarQube Scanner Analysis**

- Add build step: **Execute SonarQube Scanner**
- SonarQube scanner configuration: `LocalSonarQube`
- Analysis properties (Example):

```
sonar.projectKey=JenkinsPluginDemo
sonar.sources=src
sonar.java.binaries=target/classes
```

### ⚙️ **D) Post-Build Action (Nexus Artifact Uploader)**

- Add **Post-build Action** → **Nexus Artifact Uploader**
  - Nexus Version: Nexus 3
  - Protocol: HTTP
  - Nexus URL: `http://localhost:8081`
  - Repository: `maven-releases`
  - Credentials: (select your Nexus credentials)
  - Artifact details:
    - Group ID: `com.example`
    - Artifact ID: `JenkinsPluginDemo`
    - Version: `1.0.${BUILD_NUMBER}`
    - Packaging: `war`
    - File: `target/*.war`

### ⚙️ **E) Post-Build Action (Deploy WAR to Tomcat)**

- Add another **Post-build Action** → **Deploy war/ear to a container**
  - WAR file: `target/*.war`
  - Container: Tomcat 9.x/10.x (`http://localhost:8080`)
  - Context path: `/JenkinsPluginDemo`

---

## ▶️ **Step 10: Run the Jenkins Job**

- Click **Build Now** from the Jenkins job page.
- Observe the console output:
  - Cloning code from GitHub.
  - Maven build & unit tests.
  - SonarQube analysis.
  - Nexus artifact upload.
  - WAR deployment to Tomcat.

---

## ▶️ **Step 11: Verify Results in All Tools**

- **SonarQube** (`http://localhost:9000`)
  - Quality metrics of Java code.

- **Nexus Repository** (`http://localhost:8081`)
  - Check for uploaded WAR file in `maven-releases`.

- **Tomcat Server** (`http://localhost:8080/JenkinsPluginDemo`)
  - Check the deployed application running.

---

## 📌 **Common Troubleshooting Tips**

- **Plugin not found**: Refresh Jenkins Update Center and retry.
- **Credential errors**: Re-verify credentials in Jenkins settings.
- **Deployment failure**: Check Tomcat user permissions and Nexus repository URLs.

---

## 📚 **Summary of Plugin Purposes**

| Plugin Name               | Purpose                                 |
|---------------------------|-----------------------------------------|
| **Git**                   | Source code checkout from GitHub/Git     |
| **Maven Integration**     | Automated build and testing with Maven  |
| **SonarQube Scanner**     | Static code analysis & quality checks    |
| **Nexus Artifact Uploader**| Store build artifacts in Nexus         |
| **Deploy to Container**   | Deploy WAR/EAR to application server     |

---

## 🎓 **Learning Outcomes**

By completing this hands-on lab, students will be able to:

- Confidently manage Jenkins plugins to support CI/CD pipelines.
- Configure plugins appropriately within Jenkins.
- Automate the end-to-end lifecycle of Java web applications.

This practical guide provides real-world, hands-on experience in configuring essential Jenkins plugins, critical for effective CI/CD pipeline creation and maintenance.
