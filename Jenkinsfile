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
        step([$class: 'GitHubCommitStatusSetter', contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'Build'], statusResultSource: [$class: 'ConditionalStatusResultSource', results: [[$class: 'AnyBuildResult', message: 'Building on Jenkins CI', state: 'PENDING']]]])
        sh './build.sh'
        archiveArtifacts artifacts: 'output.bin'
        stash 'output.bin'
        step([$class: 'GitHubCommitStatusSetter', contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'Build'], statusResultSource: [$class: 'ConditionalStatusResultSource', results: [[$class: 'AnyBuildResult', message: 'Successfully built on Jenkins CI', state: 'SUCCESS']]]])
      }
    }
    stage('Test') {
      agent { label 'build && x64' }
      steps {
        unstash 'output.bin'
        stash 'output2.bin'
      }
    }
  }
  post {
    success {
      echo 'Done!'
    }
  }
}
