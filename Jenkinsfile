pipeline {
  agent {
    docker {
      image 'maven:latest'
      args '-v $HOME/.m2:/root/.m2'
    }
  }
  stages {
    stage('Build') {
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
          unstash 'app-build'
          sh 'ls -la target/surefire-reports/*.xml'
          junit 'target/surefire-reports/**/*.xml'
        }
      }
    }
    stage('Generate Allure reports') {
      steps {
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
