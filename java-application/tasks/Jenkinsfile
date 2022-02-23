pipeline {
    agent any
    tools
    {
       maven "M3"
    }
     stages {

        stage('checkout') {
          steps{

              git branch: 'main', url: 'https://github.com/prakash189/hello-java.git'

          }
      }
/// build the java application using maven ///

        stage('build code') 
          {
            steps
              {
                sh 'mvn clean install'
        
              }
         }

/// sonarqube server to scan the files ///

        stage ('Scan File') 
          {
            steps
              {
                  withSonarQubeEnv(installationName: 'sonarqubeserver', credentialsId: 'sonarqube') 
                   {
                      sh 'mvn clean package sonar:sonar -Dsonar.projectName="java-pipeline" -Dsonar.projectKey="java"'
                   }
              }
          }

/// quality gate checks ///

        stage('SQuality Gate') 
          {
            steps 
              {
                timeout(time: 5, unit: 'MINUTES') 
                  {
                     waitForQualityGate abortPipeline: true
                  }
              }
          }

        stage('upload the artifacts on nexus') 
          {
            steps 
              {
                  nexusArtifactUploader artifacts: 
                  [
                    [
                      artifactId: 'LoginWebApp', 
                      classifier: '', 
                      file: "target/LoginWebApp-3.war", 
                      type: 'war'
                    ]

                  ], 
                      credentialsId: 'nexus-cred', 
                      groupId: 'com.devops4solutions', nexusUrl: '18.138.191.148:8081', 
                      nexusVersion: 'nexus3', 
                      protocol: 'http', 
                      repository: 'java-apps-artifacts/', 
                      version: "3"
              }
          }


        stage('Ansible Deploy') {

            steps {
                
                sh "ansible-playbook main.yml "
        
            
            }
        }
        }
    
    }


