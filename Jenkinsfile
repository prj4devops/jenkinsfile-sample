pipeline {
  agent any
  stages {
    stage('build start') {
      steps {
        slackSend(message: "Build ${env.BUILD_NUMBER} Started", color: 'good', tokenCredentialId: '7b510914-fd7e-4635-ad2a-b40fa07fe316')
      }
    }      
    stage('git pull') {
        steps {
            git url: 'https://github.com/IaC-Source/GitOps.git', branch: 'main'
        }
    }
    stage('k8s deploy'){
        steps {
            kubernetesDeploy(kubeconfigId: 'kubeconfig',
                 configs: '*.yaml')
        }
    }
    
    stage('send diff') {
        steps {
        sh 'ls -al'
        script {
              def publisher = LastChanges.getLastChangesPublisher "PREVIOUS_REVISION", "SIDE", "LINE", true, true, "", "", "", "", ""
              publisher.publishLastChanges()
              def htmlDiff = publisher.getHtmlDiff()
              writeFile file: "build-diff-${env.BUILD_NUMBER}.html", text: htmlDiff
            }
        slackSend(message: """${env.JOB_NAME} #${env.BUILD_NUMBER} End
        (<${env.BUILD_URL}/last-changes|Check Last changed>)
        """, color: 'good', tokenCredentialId: '7b510914-fd7e-4635-ad2a-b40fa07fe316')             
        }
    }
    
  }
}
