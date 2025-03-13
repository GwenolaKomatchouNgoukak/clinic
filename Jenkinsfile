pipeline {
    agent any
    tools {
        maven 'm1'
        jdk 'JAVA_Home'
    }

    environment {
        BRANCH_NAME = "main"
        GIT_CRED = ""
        GIT_PROJECT_URL = "https://github.com/GwenolaKomatchouNgoukak/clinic.git"
        APP_NAME = "clinic"
        SONAQUBE_CRED = "c64d5c79-5ea7-41f3-a03a-bcb7b6e37f9d"
        SONAQUBE_INSTALLATION = "sonar"
        SCANNER_HOME = tool 'sonar-env'
        JFROG_CRED = "43657b42-1428-46be-a142-415fa85a7c70"
        ARTIFACTORY_URL = "http://54.166.164.106:8082/artifactory/Clinic_repo/"
        ARTIFACTPATH = "target/*.jar"
        ARTIFACTTARGETPATH = 'release_${BUILD_ID}.jar'
        REPO = "Clinic_repo"

    }

    stages{
        stage('Git Checkout'){
        // clone the repository
            steps{
                git branch: "${BRANCH_NAME}", \
                url: "${GIT_PROJECT_URL}"
            }
        }

        stage('Trivy Scan'){
            steps{
                 sh "trivy fs --format table -o maven_dependency.html ."
            }
        }

        stage('Unit Test'){
            steps{
                sh 'mvn clean -DskipTests'
                sh 'mvn compile -DskipTests'

            }
        }

        stage('Sonarqube Scan'){
            steps{
                withSonarQubeEnv(credentialsId: "${SONAQUBE_CRED}", \
                installationName: "${SONAQUBE_INSTALLATION}" ) {
              sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=${APP_NAME} -Dsonar.projectKey=${APP_NAME} \
                   -Dsonar.java.binaries=. '''
}
            }
        }

        stage('Quality Gate Check'){
            steps{
              script{
                 waitForQualityGate abortPipeline: false, credentialsId: "${SONAQUBE_CRED}" 
              }
            }
        }

        ```stage('Code Package'){
            steps{
                sh 'mvn package -DskipTests'
            }
        }
      
        stage('Upload Jar to Jfrog'){
            steps{
                withCredentials([usernamePassword(credentialsId: "${JFROG_CRED}", \
                 usernameVariable: 'ARTIFACTORY_USER', passwordVariable: 'ARTIFACTORY_PASSWORD')]) {
                    script {
                        // Define the artifact path and target location
                        //def artifactPath = 'target/*.jar'
                        //def targetPath = "release_${BUILD_ID}.jar"

                        // Upload the artifact using curl
                        sh """
                            curl -u ${ARTIFACTORY_USER}:${ARTIFACTORY_PASSWORD} \
                                 -T ${ARTIFACTPATH} \
                                 ${ARTIFACTORY_URL}/${REPO}/${ARTIFACTTARGETPATH}
                        """
                    }
                }
            }

        }

    }
}