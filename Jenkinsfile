pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Package') {
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
                }
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    // Find the WAR file
                    def warFile = sh(script: 'find . -name "*.war"', returnStdout: true).trim()
                    echo "Deploying ${warFile} to Tomcat..."
            
                    // Deploy WAR file to Tomcat using curl
                    sh """
                    curl -T ${warFile} http://4.180.4.233:8080/manager/text/deploy?path=/myapp -u diggijo:@SmallJoe10
                    """
                }
            }
        }
    }

    post {
        always {
            mail to: 'joseph.diggins15@gmail.com',
                 subject: "Pipeline ${currentBuild.result}",
                 body: "Pipeline ${currentBuild.result}"
        }
    }
}
