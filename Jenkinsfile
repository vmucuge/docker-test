pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'ping -c 2 localhost'
      }
    }
    stage('Test') {
      steps {
        echo 'This test is done'
      }
    }
  }
}