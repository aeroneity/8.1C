pipeline {
    agent any

    tools {
        maven 'Maven' // Make sure 'Maven' is the name of your Maven tool in Jenkins
        jdk 'jdk-11'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building the project using Maven...'
                sh 'mvn clean package'
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Running code analysis using Checkstyle...'
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Running OWASP Dependency Check...'
                sh 'mvn org.owasp:dependency-check-maven:check'
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to staging environment...'
                sh './deploy-to-staging.sh'
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo 'Running integration tests on staging...'
                sh './run-integration-tests.sh'
            }
        }

        stage('Deploy to Production') {
            steps {
                echo 'Deploying to production...'
                sh './deploy-to-production.sh'
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed. Please check the logs!'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
    }
}

