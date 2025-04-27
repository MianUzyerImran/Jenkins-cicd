pipeline {
    agent any

    tools {
        maven 'Maven 3.9.6'
        jdk 'JAVA JDK 17'
    }

    stages {
        stage('Fetch code') {
            steps {
                git branch: 'atom',
                    url: 'https://github.com/hkhcoder/vprofile-project/'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn install -DskipTests'
            }
            post {
                always {
                    echo "------------Printing the always block------------"
                }
                success {
                    echo "Archiving Artifact"
                    echo "------------Printing the success block------------"
                    archiveArtifacts artifacts: '**/*.war'
                }
                failure {
                    echo "------------Printing the failure block------------"
                }
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Checkstyle Analysis') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Sonar Code Analysis') {
            environment {
                scannerHome = tool 'sonar6.2'
            }
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh """
                    ${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=vprofile \
                    -Dsonar.projectName=vprofile \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml
                    """
                }
            }
        }

        stage('Quality Gate Check') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('UploadArtifact') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '172.31.23.121:8081',
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: 'vprofile-repo',
                    credentialsId: 'nexuslogin',
                    artifacts: [
                        [
                            artifactId: 'vproapp',
                            classifier: '',
                            file: 'target/vprofile-v2.war',
                            type: 'war'
                        ]
                    ]
                )
            }
        }
    }

    // --- Yeh post block stages ke baad likhna zaroori hai ---
    post {
        success {
            slackSend (
                channel: 'jenkins-cicd',
                color: 'good',
                message: "✅ Pipeline '${env.JOB_NAME} [#${env.BUILD_NUMBER}]' succeeded!\nURL: ${env.BUILD_URL}"
            )
        }
        failure {
            slackSend (
                channel: 'jenkins-cicd',
                color: 'danger',
                message: "❌ Pipeline '${env.JOB_NAME} [#${env.BUILD_NUMBER}]' failed!\nURL: ${env.BUILD_URL}"
            )
        }
    }
}