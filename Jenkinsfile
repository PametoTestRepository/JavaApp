pipeline {
    agent any
    
    tools{
        jdk  'jdk17'
        maven  'maven3.6'
    }
    stages {
        stage('COMPILE') {
            steps {
                sh "mvn clean compile  "
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn clean test "
            }
        }
        
        stage ('Sonarqube'){
            steps {
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-cred') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }
        
         stage ('Sonarqube Quality Gate'){
            steps {
                script{
                    waitForQualityGate abortPipeline: true, credentialsId: 'sonar-cred'
                }
            }
        }
        
        stage('Package') {
            steps {
                sh "mvn clean package "
            }
        }
        
        stage('Docker Image Building') {
             steps {
                 script {
                     withDockerRegistry(credentialsId: 'docker-cred', toolName: 'Docker') {
                         sh "docker build -t webapp3 . "
                         sh "docker run -d --name webapp2 -p 8080:80 webapp3 "   
                  }
               }
            }
        }
    }
}
