pipeline {
  agent none
  stages {
    stage('Tag') {
      when {
        expression {
          return env.BRANCH_NAME == 'release/1.0'
        }
      }
      agent { label 'master' }
      steps {
        echo 'Tagging Release Build'
      }
    }
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
        stash 'output2.bin'
        githubNotify context: 'Notification key', description: 'This is a shorted example',  status: 'SUCCESS'
      }
    }
  }
  post {
    success {
      echo 'Done!'
    }
  }
}
