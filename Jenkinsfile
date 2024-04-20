pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
            
            post {
                always {
                    // Archive JUnit-formatted test results
                    junit '**/target/surefire-reports/*.xml'
                    
                    // Record test results
                    step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/*.xml'])
                }
            }
        }
    }

    post {
        success {
            // Actions to perform if the pipeline succeeds
            echo 'Build succeeded! Deploying artifact...'
            
            // Send email notification
            emailext (
                subject: '$DEFAULT_SUBJECT',
                body: '$DEFAULT_CONTENT',
                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
                attachmentsPattern: '**/target/*.jar',
                attachLog: true,
                replyTo: '$DEFAULT_REPLYTO',
                contentType: 'text/html'
            )
        }
        failure {
            // Actions to perform if the pipeline fails
            echo 'Build failed! Not deploying artifact.'
            
            // Send email notification
            emailext (
                subject: '$DEFAULT_SUBJECT',
                body: '$DEFAULT_CONTENT',
                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
                attachmentsPattern: '**/target/*.jar',
                attachLog: true,
                replyTo: '$DEFAULT_REPLYTO',
                contentType: 'text/html'
            )
        }
    }
}
