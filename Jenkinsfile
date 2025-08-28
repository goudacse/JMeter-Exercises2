pipeline {
    agent any

    tools {
        maven 'Maven 3.9.11'
    }

    stages {
        stage('Checkout SCM') {
            steps { checkout scm }
        }

        stage('Run JMeter Tests in Parallel') {
            steps {
                bat 'mvn clean verify'
            }
        }

        stage('Publish Combined HTML Report') {
            steps {
                publishHTML(target: [
                    reportDir: 'target/jmeter/results/html',
                    reportFiles: 'index.html',
                    reportName: 'JMeter Combined HTML Report'
                ])
            }
        }

        stage('Publish Individual Reports') {
            steps {
                script {
                    def folders = findFiles(glob: 'target/jmeter/results/html/*/index.html')
                    for (file in folders) {
                        def reportDir = file.path.replaceAll('index.html','')
                        def reportName = reportDir.tokenize('\\/').last()
                        publishHTML(target: [
                            reportDir: reportDir,
                            reportFiles: 'index.html',
                            reportName: "JMeter Report - ${reportName}"
                        ])
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
