pipeline {
  agent any

  tools {
    jdk 'JDK17'
    maven 'Maven 3.9.12'
  }

  environment {
    GITHUB_TOKEN = credentials('github-token')
  }

  parameters {
    booleanParam(
      name: 'RUN_UI_TESTS',
      defaultValue: false,
      description: 'Run Selenium UI tests'
    )
  }

  stages {
    stage('Secure Step') {
      steps {
        bat 'if "%GITHUB_TOKEN%"=="" (echo TOKEN EMPTY) else (echo TOKEN SET)'
      }
    }

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build, Test & Coverage') {
      steps {
        bat 'java -version'
        bat 'mvn -v'
        bat 'mvn clean test package'
        bat 'mvn clean verify'
      }
    }

stage('SonarQube Analysis') {
  steps {
    withSonarQubeEnv('LocalSonar') {
      bat 'mvn sonar:sonar -Dsonar.projectKey=simple-java-maven-app'
    }
  }
}


        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

    stage('UI Tests (Selenium)') {
      when {
        expression { params.RUN_UI_TESTS }
      }
      steps {
        bat 'mvn -B verify -DskipUnitTests=true'
      }
    }
  }

  post {
    always {
      junit allowEmptyResults: true,
            testResults: 'target/surefire-reports/*.xml, target/failsafe-reports/*.xml'

      archiveArtifacts allowEmptyArchive: true,
            artifacts: 'target/*.jar, target/screenshots/**'

      publishHTML(target: [
            reportDir: 'target/site/jacoco',
            reportFiles: 'index.html',
            reportName: 'JaCoCo Code Coverage',
            keepAll: true,
            alwaysLinkToLastBuild: true
        ])

    }
  }
}
