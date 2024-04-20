pipeline {
    agent any

    environment {
        // Define SonarQube server credentials
        SONAR_TOKEN = credentials('sonar-token')
    }

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
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "mvn sonar:sonar -Dsonar.login=${SONAR_TOKEN}"
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [tomcat7(credentialsId: 'tomcat-credentials', url: 'http://tomcat-server:8080')], contextPath: '', war: '**/*.war'
            }
        }
    }

    post {
        success {
            emailext (
                subject: "Build Success",
                body: "Build ${currentBuild.fullDisplayName} succeeded.",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']]
            )
        }
        failure {
            emailext (
                subject: "Build Failure",
                body: "Build ${currentBuild.fullDisplayName} failed.",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']]
            )
        }
    }
}
