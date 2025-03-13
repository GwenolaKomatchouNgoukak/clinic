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
                 waitForQualityGate abortPipeline: true, credentialsId: "${SONAQUBE_CRED}" 
              }
            }
        }
    }
}