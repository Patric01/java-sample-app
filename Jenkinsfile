pipeline {
  agent { docker { image 'maven:3.9-eclipse-temurin-17' } }
  options { buildDiscarder(logRotator(numToKeepStr: '20')) }
  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Build & Test') {
      steps { sh 'mvn -B -DskipTests=false clean verify' }
      post {
        success {
          junit 'target/surefire-reports/*.xml'
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }

    // Rulăm docker build pe controller-ul Jenkins (are docker CLI + socket)
    stage('Package Docker Image') {
      agent any
      when { expression { fileExists('Dockerfile') } }
      steps {
        sh 'docker build -t java-sample-app:${BUILD_NUMBER} .'
      }
    }
  }
  post { always { echo "Build #${env.BUILD_NUMBER} finished with ${currentBuild.currentResult}" } }
}
