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
        sh './test.sh'
        stash 'output2.bin'
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
