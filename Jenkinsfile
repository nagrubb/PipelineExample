node {
  currentBuild.displayName = "${currentBuild.displayName} -> mofo"
  currentBuild.description = "bebe"
}

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
      agent { label 'gotham && builder' }
      steps {
        //Set GitHub Build commit status to pending
        step([$class: 'GitHubCommitStatusSetter',
          contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'Build'],
          statusResultSource: [$class: 'ConditionalStatusResultSource',
            results: [[$class: 'AnyBuildResult', message: 'Building on Jenkins CI', state: 'PENDING']]]])

        sh './build.sh'
        archiveArtifacts artifacts: 'output.bin'
        stash includes: 'output.bin', name: 'buildOutput'

        //Set GitHub Build commit status to success
        step([$class: 'GitHubCommitStatusSetter',
          contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'Build'],
          statusResultSource: [$class: 'ConditionalStatusResultSource',
            results: [[$class: 'AnyBuildResult', message: 'Successfully built on Jenkins CI', state: 'SUCCESS']]]])
      }
    }
    stage('PerBuildTest') {
      agent { label 'gotham && tester' }
      steps {
        //Set GitHub PerBuildTest commit status to pending
        step([$class: 'GitHubCommitStatusSetter',
          contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'PerBuildTest'],
          statusResultSource: [$class: 'ConditionalStatusResultSource',
            results: [[$class: 'AnyBuildResult', message: 'Running PerBuildTest on Jenkins CI', state: 'PENDING']]]])

        unstash 'buildOutput'
        sh './test.sh'

        //Set GitHub PerBuildTest commit status to success
        step([$class: 'GitHubCommitStatusSetter',
          contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'PerBuildTest'],
          statusResultSource: [$class: 'ConditionalStatusResultSource',
            results: [[$class: 'AnyBuildResult', message: 'Testing on Jenkins CI', state: 'SUCCESS']]]])

        //Publish test results
        step([$class: 'RobotPublisher', outputPath: '.', passThreshold: 0, unstableThreshold: 0, otherFiles: ""])
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
        withCredentials([[$class: 'UsernamePasswordMultiBinding',
          credentialsId: '681c55dd-3c24-4009-a0b5-70a52055b95f',
          usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD']]) {
          sh '''
            git config credential.username ${env.GIT_USERNAME}
            git config credential.helper "!echo password=\$GIT_PASSWORD; echo"
            GIT_ASKPASS=true git push origin --tags
            git checkout release/1.0
            git merge origin/${env.BRANCH_NAME}
            GIT_ASKPASS=true git push origin
          '''
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
