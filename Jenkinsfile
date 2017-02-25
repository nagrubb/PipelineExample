pipeline {
  agent none

  void setBuildStatus(String message, String state) {
    step([
        $class: "GitHubCommitStatusSetter",
        reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/silent-snowman/PipelineExample"],
        contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
        errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
        statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
    ]);
  }

  stages {
    stage('Build') {
      agent { label 'master' }
      steps {
        echo 'Hello Build Machine'
      }
    }
    stage('Test') {
      agent { label 'master' }
      steps {
        echo 'Hello Test Machine'
      }
    }
    stage('Package') {
      agent { label 'master' }
      steps {
        echo 'Hello Package Machine'
        setBuildStatus("Build Complete", "SUCCESS")
      }
    }
  }
}
