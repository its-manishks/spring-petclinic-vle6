pipeline {
environment {
    KUBECONFIG = '/var/lib/jenkins/.kube/config'
}
    agent any

    tools {
        maven 'Maven 3.9.6'
        jdk 'Java 17'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/its-manishks/spring-petclinic-vle6.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -Dcheckstyle.skip'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                        sh '''
            aws eks update-kubeconfig \
              --region us-east-1 \
              --name spring-cluster \
              --alias spring-cluster \
    --kubeconfig /var/lib/jenkins/.kube/config \
    --output json
        '''
        sh 'kubectl apply -f deployment.yaml --validate=false'
        sh 'kubectl apply -f service.yaml --validate=false'
            }
        }
    }

    post {
        success {
            echo 'Build and deployment succeeded!'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}

