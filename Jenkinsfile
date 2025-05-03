pipeline {
    agent any

    tools {
        maven 'Maven 3.8.4'   // Ensure this version is installed in Jenkins Global Tool Configuration
        jdk 'Java 17'         // Ensure Java 17 is also configured in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/its-manishks/spring-petclinic-vle6.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
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
    }

    post {
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}

