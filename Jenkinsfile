pipeline {
    agent none

    stages {
        stage('Install & Test') {
            agent {
                docker {
                    image 'node:16.13.1-alpine'
                    // Reuse the workspace between stages
                    reuseNode true
                }
            }
            stages {
                stage('Install dependencies') {
                    steps {
                        sh 'npm ci'
                    }
                }

                stage('Unit Tests') {
                    steps {
                        sh 'npm test -- --coverage --watchAll=false'

                        recordCoverage qualityGates: [[
                                criticality: 'NOTE',
                                integerThreshold: 40,
                                metric: 'LINE',
                                threshold: 40.0
                            ]], tools: [[
                                parser: 'COBERTURA',
                                pattern: '**/cobertura-coverage.xml'
                            ]]
                    }
                }
            }
        }

        stage('Static Code Analysis') {
            agent any

            steps {
                script {
                    def scannerHome = tool 'SonarQube Scanner'
                    withSonarQubeEnv('sonarqube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=react-bmi-app \
                            -Dsonar.sources=src
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            agent any

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
