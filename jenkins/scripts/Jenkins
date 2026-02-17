pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github-token')
    }

  tools {
    maven 'Maven 3.9.12'
  }
  parameters {
    booleanParam(
        name: 'RUN_UI_TESTS',
        defaultValue: false,
        description: 'Run Selenium UI tests'
    )
}    stages {
  stage('Secure Step') {
       steps { 
          bat 'echo "Token length is %GITHUB_TOKEN%'
      }
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
  stage('UI Tests (Selenium)') {
    when {
        expression { return params.RUN_UI_TESTS }
    }
    steps {
        bat 'mvn -B verify -DskipUnitTests=true'
    }
}
  post {
    always {
        junit allowEmptyResults: true, testResults: '''
            target/surefire-reports/*.xml,
            target/failsafe-reports/*.xml,
        archiveArtifacts allowEmptyArchive: true,
       artifacts: 'target/screenshots/**'
        '''
    }
}

    }
}

