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
        sh 'git remote rm origin'
        sh 'git remote rm origin1'
        sh 'git remote add origin "git@github.com:silent-snowman/PipelineExample.git"'
        sh 'git checkout origin/${BRANCH_NAME}'
        sh 'git checkout develop'
        sh 'git merge ${BRANCH_NAME}'
        sh 'git pull'
        sh 'git push'
      }
    }
  }
}
