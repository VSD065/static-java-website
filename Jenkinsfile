pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQube-Server' // Jenkins SonarQube server name (configured under Manage Jenkins > Configure System)
    }

    stages {
        stage('SCM Checkout') {
            steps {
                git branch: 'main', credentialsId: 'Github_Jenkins_Connection', url: 'git@github.com:VSD065/static-java-website.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Code Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    // Make sure the sonar.token is configured in Jenkins credentials
                    sh 'mvn sonar:sonar'
                }
            }
        }
    }
}
