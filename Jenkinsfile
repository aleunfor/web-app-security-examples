node {
  stage('SCM') {
    checkout scm
  }
  stage('SonarQube Analysis') {
    withMaven(maven: 'maven 3_8_5') {
      withSonarQubeEnv('SonarQube') {
        sh "mvn org.owasp:dependency-check-maven:check"
        sh "mvn clean verify sonar:sonar -Dsonar.projectKey=unforgif-test -Dsonar.dependencyCheck.htmlReportPath=./reports/dependency-check-report.html"
      }
    }
  }
  stage("Quality Gate"){
    timeout(time: 1, unit: 'MINUTES') 
    {
      waitForQualityGate abortPipeline: true
      def qg= waitForQualityGate()
      if (qg.status!= 'OK'){
        echo 'Warning! Pipeline Failed' 
        error "Pipeline aborted due to quality gate failure: ${qg.status}"
      }
    }         
    echo 'Quality Gate Passed!' 
  }
}