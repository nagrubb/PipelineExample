pipeline {
  agent none
  stages {
    stage('Build') {
      agent { label 'ubuntu' }
      steps {
        echo 'Hello Build Machine'
      }
    }
    stage('Test') {
      agent { label 'ubuntu' }
      steps {
        echo 'Hello Test Machine'
      }
    }
    stage('Package') {
      agent { label 'master' }
      steps {
        echo 'Hello Package Machine'
      }
    }
  }
}
