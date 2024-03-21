pipeline {

  agent any

  stages {
    stage('Sonarqube') {
      agent any

      environment{
        sonarpath = tool 'SonarScanner'
      }

      steps {
            echo 'Running Sonarqube Analysis..'
            withSonarQubeEnv('sonar-instavote') {
              sh "${sonarpath}/bin/sonar-scanner -Dproject.settings=sonar-project.properties -Dorg.jenkinsci.plugins.durabletask.BourneShellScript.HEARTBEAT_CHECK_INTERVAL=86400"
            }
      }
    }


    stage("Quality Gate") {
       agent any
	environment{
        sonarpath = tool 'SonarScanner'
      }
      steps {
            timeout(time: 1, unit: 'HOURS') {
                // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                // true = set pipeline to UNSTABLE, false = don't
                waitForQualityGate abortPipeline: true
            }
        }
    }
    }
  post {
    always {
      echo 'the job is complete'
      slackSend(message:"end of pipe test")
    }
  }
}
