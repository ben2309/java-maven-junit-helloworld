pipeline {

  agent any

  stages {
    stage('test') {
        agent{
          docker{
            image 'maven:3.6.1-jdk-8-slim'
            args '-v $HOME/.m2:/root/.m2'
          }
        }

      steps {
        echo 'running unit tests on worker app'
          sh 'mvn clean test'
      }
    }
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

  }
  post {
    always {
      echo 'the job is complete'
      slackSend(message:"end of pipe test")
    }
  }
}
