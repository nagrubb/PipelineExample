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
        node {
          currentBuild.displayName = "fooName"
          currentBuild.description = "fooDescription"
        }
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
        step([$class: 'GitHubCommitStatusSetter', contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'Test'], statusResultSource: [$class: 'ConditionalStatusResultSource', results: [[$class: 'AnyBuildResult', message: 'Testing on Jenkins CI', state: 'PENDING']]]])
        unstash 'output.bin'
        stash 'output2.bin'
        step([$class: 'GitHubCommitStatusSetter', contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'Test'], statusResultSource: [$class: 'ConditionalStatusResultSource', results: [[$class: 'AnyBuildResult', message: 'Testing on Jenkins CI', state: 'SUCCESS']]]])
      }
    }
    stage('Merge') {
      when {
        expression {
          return env.BRANCH_NAME == 'develop'
        }
      }
      agent { label 'master' }
      steps {
        step([$class: 'WsCleanup'])
        checkout scm
        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: '681c55dd-3c24-4009-a0b5-70a52055b95f', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD']]) {
          sh("git config credential.username ${env.GIT_USERNAME}")
          sh("git config credential.helper '!echo password=\$GIT_PASSWORD; echo'")
          sh("GIT_ASKPASS=true git push origin --tags")
          sh("git checkout release/1.0")
          sh("git merge origin/${env.BRANCH_NAME}")
          sh("GIT_ASKPASS=true git push origin")
        }
      }
    }
  }
  post {
    success {
      echo 'Done!'
    }
  }
}
