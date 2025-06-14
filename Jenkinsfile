pipeline {
    agent any
    stages {
        stage('Checkout From Git') {
            steps {
                git branch: 'main', url: 'https://github.com/Anittajose98/pet-clinic.git'
            }
        }
        stage('test') {
            steps {
                echo "This is  test stage"
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploy"
            }
        }
    }
}
