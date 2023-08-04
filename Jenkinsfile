pipeline {
    agent any
    
    environment {
        GIT_REPO = 'https://github.com/sundar474/java-web-app-docker.git'
        MAVEN_HOME = '/var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/Maven-3.8.6'
        NEXUS_URL = 'http://3.142.194.99:8081/'
        SONARQUBE_URL = 'http://3.145.92.245:9000/'
        TOMCAT_HOME = '/opt/apache-tomcat-9.0.76'
        TOMCAT_SSH_KEY = credentials('Tomcat-Server-Agent')
        TOMCAT_SERVER_IP = '3.143.218.86'
        TOMCAT_SERVER_USER = 'ec2-user'
        TOMCAT_WEBAPPS_DIR = '/opt/apache-tomcat-9.0.76/webapps'
        WAR_FILE_NAME = 'java-web-app-1.0.war'
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
                withSonarQubeEnv('Sonar Server-7.8') {
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
                    sh "mkdir -p ${env.TOMCAT_HOME}/webapps"
                    sh "cp ${warFile} ${env.TOMCAT_HOME}/webapps/"
                }
            }
        }
    }
}
