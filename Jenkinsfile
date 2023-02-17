pipeline {
  agent any
  stages {
    stage('deploy start') {
      steps {
        slackSend(message: "Deploy ${env.BUILD_NUMBER} Started"
        , color: 'good', tokenCredentialId: 'slack-key')
      }
    }      
    stage('git pull') {
      steps {
        // $(git remote get-url origin) will replace by sed command before RUN
        git url: 'https://github.com/leehoseong85/gitops.git', branch: 'main'
      }
    }
    stage('k8s deploy') {
      steps {
        kubernetesDeploy(kubeconfigId: 'kubeconfig',
                         configs: '*.yaml')
      }
      post {
        success {
          slackSend(message: """${env.JOB_NAME} #${env.BUILD_NUMBER} successed
          """, color: 'good', tokenCredentialId: 'slack-key')
        }
        failure {
          slackSend(message: """${env.JOB_NAME} #${env.BUILD_NUMBER} failed
          """, color: 'danger', tokenCredentialId: 'slack-key')
        }
      }
    }
    stage('send diff') {
      steps {
        script {
          def publisher = LastChanges.getLastChangesPublisher "PREVIOUS_REVISION", "SIDE", "LINE", true, true, "", "", "", "", ""
          publisher.publishLastChanges()
          def htmlDiff = publisher.getHtmlDiff()
          writeFile file: "deploy-diff-${env.BUILD_NUMBER}.html", text: htmlDiff
        }
        slackSend(message: """${env.JOB_NAME} #${env.BUILD_NUMBER} End
        (<${env.BUILD_URL}/last-changes|Check Last changed>)"""
        , color: 'good', tokenCredentialId: 'slack-key')
      }
    }
  }
}
