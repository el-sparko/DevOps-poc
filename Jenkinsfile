pipeline {
  agent any
  stages {
    stage('Config System') {
      steps {
        echo "${env.BRANCH_NAME}"
        echo 'Put here provisioning stuff'
        echo 'USE Ansible :)'
      }
    }
    stage('Test the System') {
      steps {
        echo 'SBT manage dependencies, just test if is reachable'
        sh 'java -version'
        sh 'sbt about'
      }
    }
    stage('Unit Tests') {
      steps {
        echo 'Tests'
      }
    }
    stage('Build') {
      steps {
        echo 'Build'
      }
    }
    stage('Notify') {
      steps {
        script {
          if (env.BRANCH_NAME != "master") {
            sh '/opt/hub-linux-386-2.3.0-pre10/bin/hub pull-request -m "$(git log -1 --pretty=%B)"'
            notifyMessage = "Pull Request Sent"
          }
          else {
            notifyMessage = "Master ready for production"
          }
        }
        
      }
    }
  }
  post {
    success {
      script {
        header = "Job <${env.JOB_URL}|${env.JOB_NAME}> <${env.JOB_DISPLAY_URL}|(Blue)>"
        header += " build <${env.BUILD_URL}|${env.BUILD_DISPLAY_NAME}> <${env.RUN_DISPLAY_URL}|(Blue)>:"
        message = "${header}\n :smiley: All test passed :smiley: $notifyMessage"
        
        author = sh(script: "git log -1 --pretty=%an", returnStdout: true).trim()
        commitMessage = sh(script: "git log -1 --pretty=%B", returnStdout: true).trim()
        message += " Commit by <@${author}> (${author}): ``` ${commitMessage} ``` "
        color = '#00CC00'
        
        slackSend(message: message, baseUrl: 'https://devops-pasquali-cm.slack.com/services/hooks/jenkins-ci/', color: color, token: 'ihoCVUPB7hqGz2xI1htD8x0F')
      }
      
      
    }
    
    failure {
      script {
        header = "Job <${env.JOB_URL}|${env.JOB_NAME}> <${env.JOB_DISPLAY_URL}|(Blue)>"
        header += " build <${env.BUILD_URL}|${env.BUILD_DISPLAY_NAME}> <${env.RUN_DISPLAY_URL}|(Blue)>:"
        message = "${header}\n :sob: The Build Failed, Release not ready for production! :sob: : ``` ${failMessage} ```\n"
        
        author = sh(script: "git log -1 --pretty=%an", returnStdout: true).trim()
        commitMessage = sh(script: "git log -1 --pretty=%B", returnStdout: true).trim()
        message += " Commit by <@${author}> (${author}): ``` ${commitMessage} ``` "
        color = '#990000'
      }
      
      echo "Message ${message}"
      slackSend(message: message, baseUrl: 'https://devops-pasquali-cm.slack.com/services/hooks/jenkins-ci/', color: color, token: 'ihoCVUPB7hqGz2xI1htD8x0F')
      
    }
    
  }
}