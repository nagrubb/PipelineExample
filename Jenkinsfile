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
        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: '681c55dd-3c24-4009-a0b5-70a52055b95f', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD']]) {
          sh 'git tag -a v1.0 -m "Jenks"'
          sh 'git push https://github.com/silent-snowman/PipelineExample.git --tags'
        }
      }
    }
  }
}
