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
    }
}