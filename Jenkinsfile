node {
    stage('SCM') {
        checkout scm
    }

    stage('Clean Package'){
        sh 'mvn clean package'
    }

    stage('OWASP Dependency-Check Vulnerabilities') {
            dependencyCheck additionalArguments: '''
                -project "web-app-security-examples"
                -o "./reports/"
                -s "./"
                -f "ALL"
                --prettyPrint''', odcInstallation: 'dependency-check'

            dependencyCheckPublisher pattern: 'dependency-check-report.html'
    }

    stage('SonarQube Analysis') {
        withMaven(maven: 'maven 3_8_5') {
            withSonarQubeEnv('SonarQube') {
                sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=web-app-security-examples -Dsonar.dependencyCheck.htmlReportPath=./reports/dependency-check-report.html'
            }
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
        echo 'Quality Gate Passed!'
    }
}
