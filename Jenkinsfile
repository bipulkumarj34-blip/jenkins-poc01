pipeline {
    agent any
    
    tools {
        maven 'maven3' // This matches the name you gave in Step 2
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5')) // Keeps only last 5 builds
    }

    stages {
        stage('Checkout') {
            steps {
                // Since we are running locally, we can use the "checkout scm" 
                // or just assume the files are there for a local pipeline.
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                echo 'Compiling and Running Tests...'
                sh 'mvn clean package'
            }
        }

        stage('Archive Artifacts') {
            steps {
                echo 'Archiving the JAR file...'
                // This saves the JAR file so you can download it from the Jenkins UI
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        always {
            // This renames the build in the UI to include the build number
            script {
                currentBuild.displayName = "#${env.BUILD_NUMBER} - ${currentBuild.result}"
            }
        }
        failure {
            echo 'Pipeline Failed! Sending notification...'
            // Note: Email requires SMTP setup in Manage Jenkins > System
            mail to: 'your-email@example.com',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Check the logs at ${env.BUILD_URL}"
        }
    }
}