pipeline {
  agent none
  stages {
    stage('Build') {
      agent { label 'ubuntu' }
      steps {
        sh './build.sh'
        archiveArtifacts artifacts: 'output.bin'
      }
    }
    stage('Test') {
      agent { label 'x64' }
      steps {
        unstash 'output.bin'
        sh './test.sh'
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
