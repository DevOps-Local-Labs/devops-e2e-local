pipeline {
    agent any

    environment {
        IMAGE = "sreemanthenaclouddevops/devops-e2e-local:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/DevOps-Local-Labs/devops-e2e-local.git'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Push Image') {

            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $IMAGE
                    docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}

// pipeline {
//     agent any

//     environment {
//         IMAGE = "sreemanthenaclouddevops/devops-demo:latest"
//     }

//     stages {

//         stage('Checkout') {
//             steps {
//                 git '<your-repo-url>'
//             }
//         }

//         stage('Build Docker Image') {
//             steps {
//                 sh 'docker build -t $IMAGE .'
//             }
//         }

//         stage('Push to DockerHub') {
//             steps {
//                 sh '''
//                 docker login -u <username> -p <password>
//                 docker push $IMAGE
//                 '''
//             }
//         }

//         stage('Deploy to Kubernetes') {
//             steps {
//                 sh '''
//                 kubectl apply -f deployment.yaml
//                 kubectl apply -f service.yaml
//                 '''
//             }
//         }
//     }
// }