pipeline {
    agent any  // Use a Jenkins agent labeled 'linux'

    environment {
        SONARQUBE_SERVER = 'sonar'  // Ensure this matches the name of your SonarQube server in Jenkins
        SONARQUBE_TOKEN = credentials('sonar')  // Replace with your SonarQube token ID from Jenkins credentials
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))  // Keep only the last 5 builds
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the code from GitHub
                git url: 'https://github.com/revendh2704/java-web-app.git', branch: 'main'  // Adjust URL and branch if needed
            }
        }

        stage('Build') {
            steps {
                // Build the project using Maven
                sh './mvnw clean install'  // Adjust if using a different build tool or command
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(installationName: "${SONARQUBE_SERVER}") {
                    // Run SonarQube analysis
                    sh './mvnw sonar:sonar -Dsonar.login=${SONARQUBE_TOKEN}'  // Adjust if needed
                }
            }
        }

        stage('Quality Gate') {
            steps {
                // Wait for SonarQube analysis report and check quality gate status
                timeout(time: 1, unit: 'HOURS') {  // Adjust timeout if needed
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace after build
            cleanWs()
        }
        success {
            echo 'Build and SonarQube analysis completed successfully.'
        }
        failure {
            echo 'Build or SonarQube analysis failed.'
        }
    }
}
