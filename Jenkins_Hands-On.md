Step-by-step, practical hands-on guide to help your students integrate **Git, Maven, Nexus, SonarQube, and Tomcat** using **Jenkins CI/CD** pipeline, assuming your Java source code resides in a GitHub repository.

---

# 🚀 Step-by-Step Hands-on Lab: CI/CD Pipeline with Jenkins, GitHub, Maven, Nexus, SonarQube, and Tomcat

---

## 🎯 **Objective**

By the end of this practical hands-on lab, students will:

- Set up Jenkins to pull Java source code from GitHub.
- Build and test Java code using Maven.
- Perform static code analysis using SonarQube.
- Store build artifacts in Nexus Repository.
- Deploy applications to Tomcat automatically.

---

## ✅ **Prerequisites**

Students must install and set up:

- **Java JDK 11+**
- **Apache Maven**
- **Apache Tomcat 10.x**
- **SonarQube**
- **Nexus OSS**
- **Jenkins**
- **Git & GitHub repository** (with existing Java/Maven app)

---

## 📌 **Step-by-Step Practical Guide**

---

## 🟢 **Step 1: Start and Verify All Tools**

Ensure each tool is running:

- **Tomcat**: `http://localhost:8080`
- **SonarQube**: `http://localhost:9000`
- **Nexus**: `http://localhost:8081`
- **Jenkins**: `http://localhost:8082`

> Adjust port numbers as needed.

---

## 🟢 **Step 2: Configure Nexus for Maven Artifacts**

- **Create Maven Hosted Repositories in Nexus:**
  - Login to Nexus (`http://localhost:8081`)
  - Go to **Repositories → Create repository**
    - Create **maven2 (hosted)**:
      - `maven-releases` (for stable artifacts)
      - `maven-snapshots` (for snapshot artifacts)

---

## 🟢 **Step 3: Configure SonarQube for Maven Analysis**

- Log into SonarQube (`http://localhost:9000`) with default credentials:
  - Username: `admin`
  - Password: `admin` (change after first login)
  
- **Create a SonarQube Project**:
  - Name: `JavaJenkinsCI`
  - Generate and save Project Key (e.g., `JavaJenkinsCI`).

---

## 🟢 **Step 4: Configure Jenkins**

- Log into Jenkins (`http://localhost:8082`)
- Install essential plugins:
  - Git
  - Maven Integration
  - SonarQube Scanner
  - Nexus Artifact Uploader
  - Deploy to Container (Tomcat)

Go to Jenkins → Manage Jenkins → Plugins → Available, search and install the above plugins.

---

## 🟢 **Step 5: Configure Jenkins Global Tool Configuration**

In Jenkins → Manage Jenkins → Global Tool Configuration:

- **JDK**
  - Name: `jdk11`
  - Set JAVA_HOME path
- **Maven**
  - Name: `maven3`
  - Automatically install Maven 3.x
- **SonarQube Scanner**
  - Name: `SonarScanner`
  - Automatically install latest version

---

## 🟢 **Step 6: Add Nexus, SonarQube, and Tomcat Credentials in Jenkins**

Go to Jenkins → Manage Jenkins → Manage Credentials:

- **Nexus Credentials**
  - ID: `nexus-credentials`
  - Username/password: `admin/admin123`

- **SonarQube Credentials**
  - ID: `sonar-token`
  - Secret text: (Create token in SonarQube under Account → Security → Generate Token)

- **Tomcat Credentials**
  - ID: `tomcat-credentials`
  - Username/password: (e.g., `tomcat/tomcat`)

---

## 🟢 **Step 7: Create and Configure Jenkins Pipeline Job**

### ① Create a new pipeline job:

- **Jenkins Dashboard → New Item → "JavaJenkinsCI-CD"**.
- Select **Pipeline**, and click OK.

### ② Configure the pipeline:

In the pipeline section, select **Pipeline script**, and insert this pipeline script:

```groovy
pipeline {
    agent any

    tools {
        jdk 'jdk11'
        maven 'maven3'
    }

    environment {
        NEXUS_URL = 'http://localhost:8081'
        SONAR_URL = 'http://localhost:9000'
        TOMCAT_URL = 'http://localhost:8080'
        SONAR_CREDENTIALS = credentials('sonar-token')
        NEXUS_CREDENTIALS = credentials('nexus-credentials')
        TOMCAT_CREDENTIALS = credentials('tomcat-credentials')
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git url: 'https://github.com/YourGitHubUsername/YourRepo.git', branch: 'main'
            }
        }

        stage('Maven Build & Unit Tests') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarScanner') {
                    sh '''
                    mvn sonar:sonar \
                      -Dsonar.projectKey=JavaJenkinsCI \
                      -Dsonar.host.url=${SONAR_URL} \
                      -Dsonar.login=${SONAR_CREDENTIALS}
                    '''
                }
            }
        }

        stage('Upload Artifacts to Nexus') {
            steps {
                sh '''
                mvn deploy:deploy-file \
                  -DgroupId=com.example \
                  -DartifactId=JavaJenkinsCI \
                  -Dversion=1.0.${BUILD_NUMBER} \
                  -Dpackaging=war \
                  -Dfile=target/*.war \
                  -DrepositoryId=nexus-credentials \
                  -Durl=${NEXUS_URL}/repository/maven-releases/
                '''
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat-credentials',
                                path: '', 
                                url: "${TOMCAT_URL}")], 
                                contextPath: 'JavaJenkinsCI', 
                                war: 'target/*.war'
            }
        }
    }
}
```

Replace:

- **GitHub URL** with your actual GitHub repo URL.
- Adjust `groupId`, `artifactId`, and credentials IDs as needed.

Save your configuration.

---

## 🟢 **Step 8: Run the Jenkins Pipeline Job**

- Click **Build Now** from the Jenkins job page.
- Monitor the pipeline execution through Jenkins UI.

---

## 🟢 **Step 9: Verify the Entire CI/CD Workflow**

- Jenkins Pipeline UI shows build stages (`Success` or `Fail`).
- Check **SonarQube UI** (`http://localhost:9000`) for Quality Analysis results.
- Check **Nexus** (`http://localhost:8081`) to confirm artifacts are uploaded.
- Verify deployment by accessing your web app via Tomcat:
```
http://localhost:8080/JavaJenkinsCI/
```

---

## 🛠 **Troubleshooting Tips**

- **Jenkins unable to pull from GitHub**: 
  - Check repository permissions or Jenkins firewall settings.
- **SonarQube scan failing**:
  - Check if SonarQube server is reachable and credentials/token correct.
- **Nexus upload failure (401 error)**:
  - Verify credentials in Jenkins and Nexus.
- **Deployment fails to Tomcat**:
  - Verify Tomcat credentials, ports, and Tomcat manager app permissions.

---

## 📚 **Learning Outcomes**

After completing this lab, students will:

- Understand CI/CD pipelines and automation.
- Gain practical experience integrating multiple tools (Jenkins, Git, Maven, SonarQube, Nexus, Tomcat).
- Experience troubleshooting real-world integration issues.

This practical guide empowers students to implement robust, automated CI/CD workflows in professional environments.
