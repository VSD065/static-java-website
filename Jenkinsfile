pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQube-Server' // Jenkins SonarQube server name (configure under "Configure System")
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
                withCredentials([string(credentialsId: 'sonarqube-user-token', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv("${SONARQUBE}") {
                        sh 'mvn sonar:sonar -Dsonar.login=$SONAR_TOKEN'
                    }
                }
            }
        }

        // Optional: Uncomment if you want to include artifact upload or deployment
        /*
        stage('Upload Artifact to Nexus') {
            steps {
                script {
                    def fileName = sh(script: "ls target/*.war", returnStdout: true).trim()
                    sh "curl -v -u admin:admin123 --upload-file ${fileName} ${NEXUS_URL}"
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def war = sh(script: "ls target/*.war", returnStdout: true).trim()
                    sh "curl --upload-file ${war} --user ${TOMCAT_CRED_USR}:${TOMCAT_CRED_PSW} ${TOMCAT_URL}/deploy?path=/static-site-java"
                }
            }
        }
        */
    }
}
