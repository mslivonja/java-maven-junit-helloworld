pipeline {
  agent none
  options{
    skipDefaultCheckout true
  }
  stages {
    stage('Init') {
      agent any
      steps {
        echo 'Initialization stage'
        // echo "Clean workspace ${WORKSPACE}"
        // cleanWs()
        echo "Checkout SCM"
        checkout scm
      }
    }
    stage('Build') {
      agent {
        docker {
          image 'maven:latest'
          args '-v $HOME/.m2:/root/.m2'
        }
      }
      steps {
        echo 'Build Java project'
	
        configFileProvider(
          [configFile(fileId: 'Telia_maven_settings', variable: 'MAVEN_SETTINGS')]){
                sh ' mvn -s $MAVEN_SETTINGS clean test'
        }
        stash includes: 'target/**', name: 'app-build'
      }
      post {
        success {
          sh 'ls -la target/surefire-reports/*.xml'
          // Generate JUnit reports
          junit 'target/surefire-reports/**/*.xml'
        }
      }
    }
    stage('Generate Allure reports') {
      agent any
      steps {
        // Generate Allure reports
        unstash 'app-build'
        script {
              allure([
                      includeProperties: false,
                      jdk: '',
                      properties: [],
                      reportBuildPolicy: 'ALWAYS',
                      results: [[path: 'target/allure-results']]
              ])
        }
      }
    }
  }
}
