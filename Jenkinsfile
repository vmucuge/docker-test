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
            
          },
          "Check for apt-get": {
            sh 'which apt-get'
            
          },
          "Install gcloud SDK": {
            sh '''# Create an environment variable for the correct distribution
export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)"

# Add the Cloud SDK distribution URI as a package source
echo "deb http://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

# Import the Google Cloud Platform public key
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

# Update the package list and install the Cloud SDK
apt-get update && apt-get install google-cloud-sdk'''
            
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