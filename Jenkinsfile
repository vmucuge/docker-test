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
        parallel(
          "Test": {
            echo 'This test is done'
            
          },
          "Test2": {
            sh 'ping -c 10 localhost'
            
          }
        )
      }
    }
    stage('Smoke Test') {
      steps {
        parallel(
          "Smoke Test": {
            sleep 10
            
          },
          "Listing all the necessary files": {
            sh '''ls /
echo $HOME'''
            
          }
        )
      }
    }
    stage('Acceptance Test') {
      steps {
        parallel(
          "Acceptance Test": {
            isUnix()
            
          },
          "Check for gcloud": {
            sh 'which cloud'
            
          }
        )
      }
    }
    stage('Deploy') {
      steps {
        sh 'echo "This software has been deployed"'
      }
    }
  }
}