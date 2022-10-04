node {
    stage('SCM') {
        checkout scm
    }

    stage('OWASP Dependency-Check Vulnerabilities') {
            dependencyCheck additionalArguments: '''
                -project "web-app-security-examples"
                -o "./reports"
                -s "./"
                -f "ALL"
                --prettyPrint''', odcInstallation: 'dependency-check'

            dependencyCheckPublisher pattern: 'dependency-check-report.html'
    }

    stage('SonarQube Analysis') {
        def scannerHome = tool 'SonarQube Scanner'
        withSonarQubeEnv('SonarQube') { // If you have configured more than one global server connection, you can specify its name
            sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=web-app-security-examples -Dsonar.dependencyCheck.htmlReportPath=./reports/dependency-check-report.html -Dsonar.dependencyCheck.jsonReportPath=./reports/dependency-check-report.json"
        }
    }

    stage('Quality Gate') {
        timeout(time: 1, unit: 'MINUTES')
    {
            waitForQualityGate abortPipeline: true
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
                echo 'Warning! Pipeline Failed'
                error "Pipeline aborted due to quality gate failure: ${qg.status}"
            }
    }
        echo 'Quality Gate Passed'
    }
}
