pipeline {
    agent any

    tools {
        maven 'maven'
    }
    environment {  
        IMAGE_NAME = "springboot"
        IMAGE_TAG  = "latest"
        TENANT_ID  = "765d1d0b-281b-4c54-bb5d-8f10bb1ecffe"
        ACR_NAME   = "jenkins1"
        ACR_LOGIN_SERVER = "${ACR_NAME}.azurecr.io"
        FULL_IMAGE_NAME = "${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}"
        RESOURCE_GROUP = "demo-rg"
        CLUSTER_NAME = "demo-aks"
        
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

        stage('Maven Package'){
            steps{
                echo 'This is Maven Package Stage'
                sh 'mvn package'
            }
        }
        stage('Docker Build') {
            steps {
              script {
                echo 'Docker Build Started'
                docker.build ("$IMAGE_NAME:$IMAGE_TAG") 
              }
            }
        }
        stage('Azure Login to ACR') {
            steps{
                 withCredentials([usernamePassword(credentialsId: 'azurespn', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                    script {
                      echo 'Azure Login Started'
                     sh '''
                      az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                      az acr login --name $ACR_NAME
                      '''
                    }
                }
            }
        }
        stage('Docker Push') {
            steps {
              script {
                echo 'Docker push Started'
                sh '''
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${FULL_IMAGE_NAME}
                docker push ${FULL_IMAGE_NAME}
                '''

              }
            }
        }
        stage('Jenkins Login to Kubernetes'){
           steps{
               withCredentials([usernamePassword(credentialsId: 'azurespn', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                script{
                    echo"Jenkins Login to Azure and kubernetes"
                    sh '''
                    az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                    az aks get-credentials --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME
                    '''
                    }
                } 
            }

        }

        stage('Deploy to kuberenetes'){
           steps{
            script{
                echo 'Deploy to kuberenetes'
                sh 'kubectl apply -f springboot-deployment.yaml'
                echo 'Deployed to kuberenetes'
            }
           }
        }
    }
}

        
    

    
