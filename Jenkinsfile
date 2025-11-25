pipeline {
  agent none

  stages {
    stage('Checkout') {
      agent { label 'built-in' }
      steps { checkout scm }
    }

    stage('Build & Test') {
      agent { docker { image 'maven:3.9-eclipse-temurin-17' } }
      steps { sh 'mvn -B -DskipTests=false clean verify' }
      post {
        success {
          junit 'target/surefire-reports/*.xml'
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
      }
    }

    stage('Package Docker Image') {
      agent { label 'built-in' }     // pe controller (are docker CLI + socket)
      when { expression { fileExists('Dockerfile') } }
      steps {
        sh 'docker version'
        sh 'docker build -t java-sample-app:${BUILD_NUMBER} .'
      }
    }
  }

  post { always { echo "Build #${env.BUILD_NUMBER} finished with ${currentBuild.currentResult}" } }
}
