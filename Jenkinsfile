pipeline {
    agent any
    stages {

        stage('Static Code Analysis') {
            steps {
                script { 
                    def scannerHome = tool 'SonarQube Scanner'
                    withSonarQubeEnv('sonarqube'){
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=react-bmi-app \
                            -Dsonar.sources=src
                        """
                    }
                }   
            }
        }
        
        stage("Quality Gate") {
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