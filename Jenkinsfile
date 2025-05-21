pipeline {
    agent any

    /*
    environment {
        SONARQUBE = 'SonarQube-Server'
        NEXUS_URL = 'http://<nexus-server>:8081/repository/maven-releases/'
        TOMCAT_URL = 'http://<tomcat-server>:8080/manager/text'
        TOMCAT_CRED = credentials('tomcat-user-pass')
    }
    */

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

        /*
        stage('Code Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh 'sonar-scanner'
                }
            }
        }

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
