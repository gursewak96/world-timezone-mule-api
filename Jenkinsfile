pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        bat 'mvn clean install'
      }
    }
    stage('Deploy') {
      steps {
        bat 'mvn clean package deploy -DmuleDeploy'
      }
    }
    stage('Postman test') {
      steps {
        bat 'newman run .\\src\\test\\resources\\world-timezone-api.postman_collection.json --disable-unicode'
      }
    }
  }
}