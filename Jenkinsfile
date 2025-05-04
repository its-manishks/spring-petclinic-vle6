pipeline {
    agent any

    environment {
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
        IMAGE = "mk283/spring-petclinic:${BUILD_NUMBER}"
        COLOR = "green" // Change to "blue" or toggle dynamically
    }

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

        stage('Build & Package') {
            steps {
                sh 'mvn clean package -Dcheckstyle.skip -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u mk283 --password-stdin'
                    sh 'docker push $IMAGE'
                }
            }
        }

        stage('Update Kubeconfig') {
            steps {
                sh '''
                aws eks update-kubeconfig \
                  --region us-east-1 \
                  --name spring-cluster \
                  --alias spring-cluster \
                  --kubeconfig /var/lib/jenkins/.kube/config \
                  --output json

                sed -i 's/client.authentication.k8s.io\\/v1alpha1/client.authentication.k8s.io\\/v1beta1/g' /var/lib/jenkins/.kube/config
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment-$COLOR.yaml --validate=false'
            }
        }

        stage('Switch Service') {
            steps {
                input message: "Switch traffic to $COLOR version?"
                sh """
                kubectl patch service petclinic-service -p '{"spec": {"selector": {"app": "petclinic", "color": "$COLOR"}}}'
                """
            }
        }
    }

    post {
        success {
            echo 'Blue-Green deployment successful!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

