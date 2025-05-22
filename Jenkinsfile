pipeline {
    agent any

    tools {
        maven 'Maven 3.8.1' // Set your installed Maven version in Jenkins
    }

    environment {
        STAGING_SERVER = "ec2-user@staging-server-ip"
        PROD_SERVER = "ec2-user@production-server-ip"
        ARTIFACT = "target/myapp.jar"
    }

    stages {

        stage('Build') {
            steps {
                echo 'Building the project...'
                sh 'mvn clean package'
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo 'Running unit and integration tests...'
                sh 'mvn test'
                // Optional: sh 'newman run tests/collection.json' (for Postman API tests)
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Running SonarQube analysis...'
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Running OWASP Dependency-Check...'
                sh 'mvn org.owasp:dependency-check-maven:check'
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to staging server...'
                sh '''
                    scp ${ARTIFACT} ${STAGING_SERVER}:/home/ec2-user/
                    ssh ${STAGING_SERVER} "nohup java -jar /home/ec2-user/myapp.jar > output.log 2>&1 &"
                '''
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo 'Running Selenium tests on staging...'
                // Example: Run Selenium test suite
                sh 'mvn test -Dtest=StagingIntegrationTests'
            }
        }

        stage('Deploy to Production') {
            steps {
                echo 'Deploying to production server...'
                sh '''
                    scp ${ARTIFACT} ${PROD_SERVER}:/home/ec2-user/
                    ssh ${PROD_SERVER} "pkill -f myapp.jar || true"
                    ssh ${PROD_SERVER} "nohup java -jar /home/ec2-user/myapp.jar > output.log 2>&1 &"
                '''
            }
        }

    }

    post {
        failure {
            echo 'Pipeline failed. Sending alert...'
            // Optionally send Slack/Email notifications here
        }
    }
}
