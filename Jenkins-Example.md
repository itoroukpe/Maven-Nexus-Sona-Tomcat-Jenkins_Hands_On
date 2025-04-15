Here’s a **Jenkins scripted pipeline** to automate the CI/CD process using:

- GitHub Repo: `https://github.com/itoroukpe/maven-web-application`
- Maven for build
- Nexus for artifact storage
- Tomcat for deployment
- Email notification for approval (with an 8-hour timeout)

---

### 🔧 **Required Jenkins Plugins**:
Ensure these plugins are installed:

1. **Pipeline** (already built-in)
2. **Pipeline: GitHub**
3. **Maven Integration plugin**
4. **Nexus Artifact Uploader**
5. **Deploy to container plugin**
6. **Email Extension Plugin**
7. **Pipeline: Input Step**

**To install plugins**:
- Go to: `Manage Jenkins` → `Manage Plugins` → `Available`
- Search and install the above plugins
- Restart Jenkins if prompted

---

### 📜 **Scripted Jenkins Pipeline**

```groovy
pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/itoroukpe/maven-web-application.git'
        MAVEN_HOME = tool 'Maven 3' // Define in Jenkins Global Tools
        NEXUS_URL = 'http://<nexus-ip>:8081/repository/maven-releases/'
        NEXUS_CREDENTIALS_ID = 'nexus-creds'
        WAR_FILE = 'target/maven-web-application.war'
        DEPLOY_SERVER = 'http://<tomcat-ip>:8080/manager/text'
        DEPLOY_CREDENTIALS_ID = 'tomcat-creds'
        EMAIL_RECIPIENTS = 'team@example.com'
    }

    stages {
        stage('Clone Source Code') {
            steps {
                git url: "${env.GIT_REPO}"
            }
        }

        stage('Build with Maven') {
            steps {
                withEnv(["PATH+MAVEN=${MAVEN_HOME}/bin"]) {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Upload to Nexus') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${env.NEXUS_URL}",
                    groupId: 'com.itoro',
                    version: '1.0.${BUILD_NUMBER}',
                    repository: 'maven-releases',
                    credentialsId: "${env.NEXUS_CREDENTIALS_ID}",
                    artifacts: [
                        [artifactId: 'maven-web-application', classifier: '', file: "${env.WAR_FILE}", type: 'war']
                    ]
                )
            }
        }

        stage('Approval for Deployment') {
            steps {
                script {
                    def userInput = input message: 'Approve deployment to Tomcat?', 
                        ok: 'Deploy',
                        submitterParameter: 'approver',
                        parameters: [
                            [$class: 'TextParameterDefinition', defaultValue: 'yes', description: 'Type "yes" to approve', name: 'approval']
                        ], timeout: 8 * 60 * 60, // 8 hours
                        timeoutMessage: 'Deployment timed out without approval.'

                    if (userInput.approval != 'yes') {
                        error("Deployment not approved.")
                    }
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [tomcat8(credentialsId: "${env.DEPLOY_CREDENTIALS_ID}", path: '', url: "${env.DEPLOY_SERVER}")],
                       contextPath: 'maven-web-application',
                       war: "${env.WAR_FILE}"
            }
        }
    }

    post {
        success {
            emailext (
                subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "The build was successful. \n\nCheck console: ${env.BUILD_URL}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                to: "${env.EMAIL_RECIPIENTS}"
            )
        }
        failure {
            emailext (
                subject: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "The build has failed. \n\nCheck console: ${env.BUILD_URL}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                to: "${env.EMAIL_RECIPIENTS}"
            )
        }
    }
}
```

---

### ✅ **Final Setup Checklist**:

1. Add **Maven** in `Manage Jenkins → Global Tool Configuration → Maven`.
2. Add **credentials** for Nexus (`nexus-creds`) and Tomcat (`tomcat-creds`) in `Manage Jenkins → Credentials`.
3. Confirm your **Tomcat manager app** is configured to allow WAR uploads.
4. Set up **SMTP settings** in `Manage Jenkins → Configure System` to allow email notifications.

---

Would you like this pipeline wrapped as a Jenkinsfile for your repo, or would you like me to generate a diagram of this flow too?
