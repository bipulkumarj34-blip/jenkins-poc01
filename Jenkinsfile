pipeline {
    agent any
    
    tools {
        maven 'maven3'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                echo 'Compiling and Running Maven Tests...'
                sh 'mvn clean package'
            }
        }

        stage('Archive Artifacts') {
            steps {
                echo 'Archiving the JAR file...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        always {
            script {
                currentBuild.displayName = "#${env.BUILD_NUMBER} - ${currentBuild.result}"
            }
        }
        failure {
            echo 'Pipeline Failed! Sending notification...'
            mail to: 'bjha@nisum.com',
                 subject: "Build Failed: ${env.JOB_NAME} [Build #${env.BUILD_NUMBER}]",
                 body: """The build failed at stage: ${env.STAGE_NAME}
                       
                       View the full console output here: ${env.BUILD_URL}console
                       
                       - Jenkins Automator""",
                 from: 'jenkins-mac@noreply.com'
        }
    }
}
