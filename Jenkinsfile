pipeline {
  agent none
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
      agent { label 'build && x64' }
      steps {
        unstash 'output.bin'
        sh './test.sh'
        stash 'output2.bin'
      }
    }
    stage('Merge') {
      agent { label 'master' }
      steps {
        sh 'git checkout develop'
        sh 'git merge origin/${BRANCH_NAME}'
        sh 'sudo git remote rm origin'
        sh 'sudo git remote add origin "git@github.com:silent-snowman/PipelineExample.git"'
        sh 'git push'
      }
    }
  }
}
