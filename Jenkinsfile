@Library('b12') _
pipeline {
   agent any
   options {
      timestamps()
      disableConcurrentBuilds(abortPrevious: true)
   }
   tools {
      nodejs 'node-18'
   }
   environment {
      COMMITER_EMAIL = "${sh(script: 'git --no-pager show -s --format=\'%ae\'', returnStdout: true).trim()}"
      PROJECT_NAME = "${sh(script: 'cat sonar-project.properties | grep \'sonar.projectKey\' | awk -F\'=\' \'{print $2}\'', returnStdout: true).trim()}"
      PROJECT_NAME_VERBOSE = "${sh(script: 'cat sonar-project.properties | grep \'sonar.projectName\' | awk -F\'=\' \'{print $2}\'', returnStdout: true).trim()}"
   }
   stages {
      stage('SonarQube Analysis') {
         environment {
            SCANNER_HOME = tool 'SonarScanner'
            PATH = "${env.SCANNER_HOME}/bin:${env.PATH}"
         }
         steps {
            catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
               sonarQube()
               script { env.SONARQUBE_RESULT = true }
            }
         }
      }
   }
   post {
      unsuccessful {
         email("unsuccessful")
      }
   }
}
