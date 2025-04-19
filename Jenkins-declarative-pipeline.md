Here’s a **step-by-step guide** to set up a **declarative Jenkins pipeline** that integrates:

✅ GitHub Repo: `https://github.com/itoroukpe/maven-web-application.git`  
✅ SonarQube for code quality  
✅ Nexus for artifact repository  
✅ Apache Tomcat for deployment  

---

## 🔧 **1. Prerequisites**

Before starting, ensure you have:

- Jenkins installed and running  
- Git, Maven installed on Jenkins server  
- SonarQube and Nexus running (can be local or remote)  
- Tomcat installed and accessible  
- GitHub repo access  

---

## 🛠️ **2. Install Required Jenkins Plugins**

Go to **Manage Jenkins → Plugin Manager → Available**, and install:

- **Pipeline**  
- **Git**  
- **Maven Integration**  
- **SonarQube Scanner**  
- **Nexus Artifact Uploader**  
- **Deploy to container**  
- **Credentials Binding**  
- **Publish Over SSH** (if needed for Tomcat deploy)

---

## 🔐 **3. Configure Global Tools**

Go to **Manage Jenkins → Global Tool Configuration**:

- **Maven**: Add installation (e.g., Maven 3.8.7)  
- **JDK**: Set up Java version (e.g., Java 11 or 17)  
- **Git**: Ensure git path is configured  
- **SonarQube**: Add under **Configure System → SonarQube servers**  
  - Name: `SonarQube`  
  - Server URL: `http://<your-sonarqube-host>:9000`  
  - Add authentication token  

- **Nexus Credentials**: Go to **Manage Credentials → Global → Add Credentials**  
  - ID: `nexus-creds`  
  - Type: Username/Password  
  - Add Nexus username/password  

- **Tomcat Deployment**:
  - Go to **Manage Jenkins → Configure System**
  - Under "Deploy to container", configure Tomcat credentials and endpoint

---

## 📁 **4. Create a Declarative Pipeline Job**

1. Go to **New Item → Pipeline**
2. Name: `Maven-WebApp-CICD`
3. Select **Pipeline**, click OK
4. Scroll down to **Pipeline Definition**
   - Choose **Pipeline script from SCM**
   - SCM: Git  
   - Repo URL: `https://github.com/itoroukpe/maven-web-application.git`  
   - Branch: `main` or `master`
   - Script Path: `Jenkinsfile` (or adjust if in a subfolder)

---

## 📄 **5. Jenkinsfile (Declarative Pipeline Script)**

Create a `Jenkinsfile` in the root of your GitHub repo with the following:

```groovy
pipeline {
    agent any

    tools {
        maven 'Maven 3.8.7'
        jdk 'Java 17'
    }

    environment {
        SONARQUBE = 'SonarQube' // Jenkins configured name
        NEXUS_URL = 'http://<your-nexus-host>:8081/repository/maven-releases/'
        NEXUS_CREDENTIAL_ID = 'nexus-creds'
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/itoroukpe/maven-web-application.git'
            }
        }

        stage('Code Quality - SonarQube') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

        stage('Build Artifact') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Upload to Nexus') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '<your-nexus-host>:8081',
                    groupId: 'com.itoro.maven',
                    version: '1.0.0',
                    repository: 'maven-releases',
                    credentialsId: "${NEXUS_CREDENTIAL_ID}",
                    artifacts: [[artifactId: 'maven-web-application',
                                 classifier: '',
                                 file: 'target/maven-web-application.war',
                                 type: 'war']]
                )
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'tomcat-creds', 
                                           path: '', 
                                           url: 'http://<your-tomcat-host>:8080')],
                       war: 'target/maven-web-application.war'
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
```

---

## 🧪 **6. Trigger & Monitor**

- Click **Build Now** on Jenkins job  
- Monitor the logs from **Console Output**  
- Verify:
  - SonarQube dashboard has quality report  
  - Nexus contains the `WAR` in `maven-releases`  
  - Tomcat has deployed the app and it’s accessible  

---

## 🧼 **Optional Enhancements**

- Add `input` stage for manual approval before deploy  
- Add email or Slack notifications  
- Add test stages (`unit`, `integration`, etc.)  
- Add versioning from Git tags or timestamp  

---

