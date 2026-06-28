pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Code-Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('sonar-scanner') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectName=Santa \
                    -Dsonar.projectKey=Santa \
                    -Dsonar.java.binaries=.
                    '''
                }
            }
        }

        stage('Code-Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh 'docker build -t santa123 .'
                    }
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh 'docker tag santa123 vinodk99/santa123:latest'
                        sh 'docker push vinodk99/santa123:latest'
                    }
                }
            }
        }

    }

    post {
        always {
            emailext(
                subject: "Pipeline Status: ${currentBuild.currentResult} - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                <html>
                <body>
                    <h2>Jenkins Pipeline Notification</h2>

                    <b>Status:</b> ${currentBuild.currentResult}<br>
                    <b>Job:</b> ${env.JOB_NAME}<br>
                    <b>Build Number:</b> ${env.BUILD_NUMBER}<br>
                    <b>Build URL:</b>
                    <a href="${env.BUILD_URL}">${env.BUILD_URL}</a>
                </body>
                </html>
                """,
                mimeType: 'text/html',
                to: 'kumarkvinod723@gmail.com'
            )
        }
    }
}
