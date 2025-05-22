pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQube-Server' // Jenkins SonarQube server name
        NEXUS_URL = 'http://13.200.112.50:8081/repository/vsd-maven-repo/'
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
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv("${SONARQUBE}") {
                        sh 'mvn sonar:sonar -Dsonar.token=$SONAR_TOKEN'
                    }
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh '''
                        mvn deploy \
                        -DaltDeploymentRepository=nexus::default::$NEXUS_URL \
                        -Dnexus.username=$NEXUS_USER \
                        -Dnexus.password=$NEXUS_PASS
                    '''
                }
            }
        }
    }
}
