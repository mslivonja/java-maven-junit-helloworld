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
  post {
    success {
      // send to email
      emailext (
          subject: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' status",
          body: """<p>Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' finished succesfully:</p>
            <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
          to: "matija.slivonja@ericsson.com"
        )
    }
  }
}
