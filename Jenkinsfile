pipeline {
    agent any

    environment {
        TOMCAT_URL = 'http://4.180.4.233:8080/'
        TOMCAT_USER = 'diggijo'
        TOMCAT_PASS = '@SmallJoe10'
    }

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
                sh '''
                curl -T **/target/*.war ${TOMCAT_URL}/manager/text/deploy?path=/myapp -u ${TOMCAT_USER}:${TOMCAT_PASS}
                '''
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
