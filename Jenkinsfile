Pipeline {
  agent any
  stages {
    stage( 'Checkout') {
      steps { checkout scm }
    }
    stage( 'JMeter' ) {
      steps {
        bat '''
        if exist logs rmdir /s /q logs
        if exist html rmdir /s /q html
        mkdir logs
        mkdir html
        mkdir html\\report
        "C:\\Users\\shyam\\OneDrive\\Learnings\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -n -t PostManEchoAPI_GetandPostData.jmx -l logs/results.jtl -e -o html/report
        '''
      }
    }
    stage('Performance Gates' ) {
      steps {
        perfReport (
          sourceDataFiles: 'logs/results.jtl',
          errorFailedTreshold: 1.0,
          errorUnstableTreshold: 0.0,
          // Double numbers (no quotes!)
          relativeFailedTresholdPositive: 1.3,
          relativeUnstableTresholdPositive: 1.2,
          modeOfTreshold: true,
          configType: 'ART'
          )
      }
    }
    stage('Reports') {
      steps {
        perfReport sourceDataFiles: 'logs/results.jtl'
        publishHTML(
          target: [
            reportDir: 'html/report',
            reportFiles: 'index.html',
            reportName: 'JMeter HTML Report',
            keepAll: true,
            alwaysLinkToLastBuild: true,
            allowMissing: false
            ]
          )
      }
    }
  }
  post {
    always {
      archiveArtifacts 'logs/results.jtl, html/report/**'
    }
  }
}

        
