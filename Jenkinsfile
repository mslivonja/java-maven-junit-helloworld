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
	
        configFileProvider(
          [configFile(fileId: 'Telia_maven_settings', variable: 'MAVEN_SETTINGS')]){
                sh ' mvn -s $MAVEN_SETTINGS clean test'
        }
      }
      post {
        success {
          junit 'target/surfire-reports/**/*.xml'
        }
      }
    }
  }
}
