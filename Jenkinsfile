pipeline {
  environment {
    color = "good"
  }
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
    stage('k8s deploy'){
      steps {
        kubernetesDeploy(kubeconfigId: 'kubeconfig',
                         configs: '*.yaml')
      }
      post {
        success {
          environment {
            color = "good"
          }
        }
        failure {
          environment {
            color = "danger"
          }
        }
        always {
          slackSend(message: """${env.JOB_NAME} #${env.BUILD_NUMBER} End
          """, color: "${color}", tokenCredentialId: 'slack-key')
        }
      }
    }
    stage('deploy end') {
      steps {
        slackSend(message: """${env.JOB_NAME} #${env.BUILD_NUMBER} End
        """, color: 'good', tokenCredentialId: 'slack-key')
      }
    }
  }
}
