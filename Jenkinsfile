pipeline {
    agent any
    
    tools {
        ant 'Ant 1.10'  // Make sure you have Ant configured in Jenkins
    }
    
    parameters {
        choice(
            name: 'TEST_MODE',
            choices: ['run-all', 'run-parallel', 'run-end-to-end', 'run-add-products', 'run-add-pet', 'run-add-user'],
            description: 'Select which test(s) to run'
        )
        // No thread/ramp-up parameters - using values from JMX files
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Verify Tools') {
            steps {
                bat 'ant -version'
                bat 'echo %JMETER_HOME%'
                bat 'dir "Test Plans"'
            }
        }
        
        stage('Run JMeter Tests') {
            steps {
                bat """
                    ant ${params.TEST_MODE}
                """
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'jmeter-reports/**/*', fingerprint: true
            perfReport sourceDataFiles: 'jmeter-reports/jtl/*.jtl'
            publishHTML(target: [
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'jmeter-reports/html/combined-*',
                reportFiles: 'index.html',
                reportName: 'JMeter Combined Report'
            ])
        }
    }
}
