pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQube-Server'
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
                        cat > settings.xml <<EOF
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">
  <servers>
    <server>
      <id>nexus</id>
      <username>${NEXUS_USER}</username>
      <password>${NEXUS_PASS}</password>
    </server>
  </servers>
</settings>
EOF

                        mvn deploy -DskipTests -s settings.xml
                    '''
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'tomcat-credentials', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASS')]) {
                    sh """
                        curl -v --upload-file target/static-site-java-1.0.war \\
                             --user $TOMCAT_USER:$TOMCAT_PASS \\
                             http://15.207.96.47:8080/manager/text/deploy?path=/static-site-java&update=true
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and deployment succeeded!'
        }
        failure {
            echo '❌ Something went wrong during the pipeline.'
        }
    }
}
