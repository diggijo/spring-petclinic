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
                sh '''
                curl -T **/target/*.war http://4.180.4.233:8080/manager/text/deploy?path=/myapp -u diggijo:@SmallJoe10
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
