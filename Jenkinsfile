pipeline {
  agent any
  stages {
    stage('git pull') {
      steps {
        // https://github.com/leehoseong85/gitops.git will replace by sed command before RUN
        git url: 'https://github.com/leehoseong85/gitops.git', branch: 'main'
      }
    }
    stage('k8s deploy'){
      steps {
        kubernetesDeploy(kubeconfigId: 'kubeconfig',
                         configs: '*.yaml')
      }
    }    
  }
}
