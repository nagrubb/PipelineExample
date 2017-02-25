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
        step([$class: 'GitHubCommitStatusSetter', contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'jenkins-test']])
      }
    }
  }
  post {
    success {
      echo 'Done!'
    }
  }
}
