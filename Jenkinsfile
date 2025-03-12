pipeline {
    agent any
    tools {
        maven 'm1'
        jdk 'JAVA_Home'
    }

    environment {
        BRANCH_NAME = ""
        GIT_CRED = ""
        GIT_PROJECT_URL = ""

    }

    stages{
        stage('Git Checkout'){
        // clone the repository
            steps{
                git branch: "${BRANCH_NAME}", \
                url: "${GIT_PROJECT_URL}"
            }
        }
    }
}