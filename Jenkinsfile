pipeline {
    agent any
    
    environment {
        SERVICE_NAME = "arca-pos-transaction-router-service"
        ORGANIZATION_NAME = "wasp-networks"
        DOCKERHUB_USERNAME = "frankisinfotech"
        REPOSITORY_TAG = "${DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
    }
    
    stages {
//         stage ('Preparing') {
//             steps {
//                 cleanWs()
//                 git credentialsId: 'Github', url: "git@github.com:wasp-networks/Arca.UserManagement.git" //${ORGANIZATION_NAME}/${SERVICE_NAME}"
//             }
//         }
        
        stage ('Build and Push Image') {
            steps {
                 withDockerRegistry([credentialsId: 'DOCKERHUB_USERNAME', url: ""]) {
                   sh 'docker build -t ${REPOSITORY_TAG} .'
                   sh 'docker push ${REPOSITORY_TAG}'          
            }
          }
       }
        stage("Install kubectl"){
            steps {
                sh """
                    curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
                    chmod +x ./kubectl
                    ./kubectl version --client
                """
            }
        }
        
        stage ('Deploy to Cluster') {
            steps {
                sh "aws eks update-kubeconfig --region eu-west-1 --name switch-arca-qa-cluster"
                sh " envsubst < ${WORKSPACE}/deploy.yaml | ./kubectl apply -f - "
            }
        }
    }
}
