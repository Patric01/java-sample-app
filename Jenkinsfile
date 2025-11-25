pipeline {
  agent { docker { image 'maven:3.9-eclipse-temurin-17' } }
  options {
    ansiColor('xterm')
    buildDiscarder(logRotator(numToKeepStr: '20'))
    timestamps()
  }
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Build & Test') {
      steps { sh 'mvn -B -DskipTests=false clean verify' }
      post {
        success {
          junit 'target/surefire-reports/*.xml'
          script {
            if (fileExists('target/site/jacoco/index.html')) {
              publishHTML(target: [reportDir: 'target/site/jacoco', reportFiles: 'index.html', reportName: 'JaCoCo Coverage'])
            }
          }
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }
    stage('Package Docker Image') {
      when { expression { fileExists('Dockerfile') } }
      steps { sh 'docker build -t java-sample-app:${BUILD_NUMBER} .' }
    }
  }
  post { always { echo "Build #${env.BUILD_NUMBER} finished with ${currentBuild.currentResult}" } }
}
