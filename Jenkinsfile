pipeline {
    agent any

    tools {
        maven 'Maven 3.8.4' // Or whatever is installed in Jenkins
        jdk 'jdk17'
    }

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/spring-projects/spring-petclinic.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}

