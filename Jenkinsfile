pipeline {
    agent any
    
    environment {
        GIT_REPO = 'https://github.com/sundar474/java-web-app-docker.git'
        MAVEN_HOME = 'Maven-3.8.6'
        NEXUS_URL = 'http://3.142.194.99:8081/'
        SONARQUBE_URL = 'http://3.145.92.245:9000/'
        TOMCAT_HOME = '/opt/apache-tomcat-9.0.76/webapps'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: GIT_REPO
            }
        }
        
        stage('Build') {
            steps {
                script {
                    def mavenCmd = "${env.MAVEN_HOME}/bin/mvn"
                    sh "${mavenCmd} clean package"
                }
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    script {
                        def mavenCmd = "${env.MAVEN_HOME}/bin/mvn"
                        sh "${mavenCmd} sonar:sonar"
                    }
                }
            }
        }
        
        stage('Deploy to Nexus') {
            steps {
                script {
                    def mavenCmd = "${env.MAVEN_HOME}/bin/mvn"
                    sh "${mavenCmd} deploy -Dmaven.deploy.skip=true"
                }
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFile = sh(returnStdout: true, script: 'ls target/*.war').trim()
                    sh "cp ${warFile} ${env.TOMCAT_HOME}/webapps/"
                }
            }
        }
    }
}
