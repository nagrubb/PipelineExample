pipeline {
  agent none
  if (env.BRANCH_NAME != 'master') {
    stages {
      stage('Build') {
        agent { label 'ubuntu' }
        steps {
          sh './build.sh'
          archiveArtifacts artifacts: 'output.bin'
          stash 'output.bin'
        }
      }
      stage('Test') {
        agent { label 'x64' }
        steps {
          unstash 'output.bin'
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
}
