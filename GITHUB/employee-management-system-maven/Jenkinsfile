pipeline {
    agent any

    tools {
        maven 'maven-3.9.6' 
        jdk 'jdk-17' 
    }

    environment {
        GIT_REPO_URL = 'https://github.com/eai-12345/employee-management-system-maven.git'
        DOCKER_IMAGE = "subhashis2022/employee-management-system-maven"
        DOCKER_TAG = "${params.ENV}-${env.BUILD_ID}"
        DOCKER_REGISTRY_CREDENTIALS_ID = 'docker-auth'
        GITHUB_CREDENTIALS_ID = 'github-auth'
    }

    parameters {
        choice(
            name: 'BRANCH_NAME', 
            choices: ['main', 'develop', 'feature-branch'], 
            description: 'Choose the branch to build'
        )
        booleanParam(
            name: 'RUN_TESTS', 
            defaultValue: true, 
            description: 'Check if you want to run tests'
        )
        booleanParam(
            name: 'RUN_PMD_ANALYSIS', 
            defaultValue: true, 
            description: 'Check if you want to run PMD analysis'
        )
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    git branch: "${params.BRANCH_NAME}", url: "${GIT_REPO_URL}", credentialsId: 'github-auth'
                }
            }
        }
        stage('Maven Clean') {
            steps {
                withMaven(maven: 'maven-3.9.6') {
                    sh 'mvn clean'
                }
            }
        }
        stage('Maven Build') {
            steps {
                withMaven(maven: 'maven-3.9.6') {
                    sh 'mvn package'
                }
            }
        }
        stage('Maven Test') {
            when {
                expression { params.RUN_TESTS }
            }
            steps {
                withMaven(maven: 'maven-3.9.6') {
                    sh 'mvn test'
                }
            }
        }
        stage('Jacoco Test Report') {
            steps {
                withMaven(maven: 'maven-3.9.6') {
                    sh 'mvn jacoco:report'
                }
            }
            post {
                always {
                    jacoco execPattern: '**/target/jacoco.exec', classPattern: '**/target/classes', sourcePattern: '**/src/main/java'
                }
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
