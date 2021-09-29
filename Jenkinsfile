pipeline {
  agent {
    docker {
      image 'maven:latest'
    }
  }
  stages {
    stage('Build') {
      steps {
        echo 'Build Java project'
        sh ' mvn clean test'
      }
      post {
        success {
          junit 'target/surfire-reports/**/*.xml'
        }
      }
    }
  }
}
