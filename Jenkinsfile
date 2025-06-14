pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('Checkout From Git') {
            steps {
                git branch: 'main', url: 'https://github.com/Anittajose98/pet-clinic.git'
            }
        }
        stage('Maven compile') {
            steps {
                echo "This is maven compile stage"
                sh "mvn compile"
            }
        }
        stage('Maven Test') {
            steps {
                echo "This is maven Test stage"
                sh "mvn test"
            }
        }
        stage('File scanning by Trivy') {
            steps {
                echo "Trivy scanning"
                sh 'trivy fs --format table --output trivy-report.txt --severity HIGH,CRITICAL .'
            }
        }
        stage('Sonar Analysis') {
            environment {
                SCANNER_HOME = tool 'Sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.organization=anittajose \
                        -Dsonar.projectName=springboot-pet \
                        -Dsonar.projectKey=anittajose_springboot-pet \
                        -Dsonar.java.binaries=. \
                        -Dsonar.exclusions=**/trivy-report.txt
                    '''
                }
            }
        }
    }
}
