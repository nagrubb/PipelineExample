pipeline {
  agent none
  stages {
    stage('Build') {
      agent { label 'master' }
      steps {
        echo 'Hello Build Machine'
      }
    }
    stage('Test') {
      agent { label 'master' }
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
