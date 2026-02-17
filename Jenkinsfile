pipeline {
  agent any

  tools {
    maven 'Maven 3.9.12'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build and Test') {
      steps {
        bat 'mvn -v'
        bat 'mvn clean test package'
      }
    }
  }
      post {
        always {
            junit 'target/surefire-reports/*.xml'
        }
    }
}

