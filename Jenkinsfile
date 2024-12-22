pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "nodeJs-app"
        RELEASE = "1.0.0"
        DOCKER_USER = "khaoulaabdessamad"
        DOCKER_PASS = 'dockerHub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/AbdessamadKhaoula/pipelineCI-CD-nodejs.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=nodeJs-app-CI \
                    -Dsonar.projectKey=nodeJs-app-CI'''
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonarQube-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh """
                export TRIVY_DB_REPO=ghcr.io/aquasecurity/trivy-db
                trivy fs . > trivyfs.txt """
             }
         }
}
	    // stage("Build & Push Docker Image") {
        //      steps {
        //          script {
        //              docker.withRegistry('',DOCKER_PASS) {
        //                  docker_image = docker.build "${IMAGE_NAME}"
        //              }
        //              docker.withRegistry('',DOCKER_PASS) {
        //                  docker_image.push("${IMAGE_TAG}")
        //                  docker_image.push('latest')
        //              }
        //          }
        //      }
        //  }
	    // stage("Trivy Image Scan") {
        //      steps {
        //          script {
	    //           sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image AbdessamadKhaoula/pipelineCI-CD-nodejs:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table > trivyimage.txt')
        //          }
        //      }
         }
}