pipeline {
    agent any
    tools{
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
        stage('Deploy') {
            steps {
                echo "Deploy"
            }
        }
    }
}
