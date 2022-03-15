pipeline {
    agent any
    tools
    {
       maven "M3"
    }
    parameters{
      string(name: 'Nexus_server_url', description: 'Enter the nexus server url' )
      string(name: 'Sonarqube_server_name', description: 'Enter the sonarqube server name' )
      string(name: 'Sonarqube_project_name', description: 'Enter the project name created on sonar server' )
      string(name: 'Sonar_project_key', description: 'Enter the sonarqube project key' )
      string(name: 'Artifacts_path', description: 'Enter the artifacts path' )
      string(name: 'Nexus_repo_url', description: 'Enter the nexus repository url' )

    }
     stages {

        stage('checkout') {
          steps{

              git branch: 'master', url: 'https://github.com/prakash189/cicd-pipeline-deploy-java-using-ansible-plyabook.git'

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
                  withSonarQubeEnv(installationName: '${Sonarqube_server_name}', credentialsId: 'sonar-creds') 
                   {
                      sh 'mvn clean package sonar:sonar -Dsonar.projectName="${Sonarqube_project_name}" -Dsonar.projectKey="${Sonar_project_key}"'
                   }
              }
          }

// /// quality gate checks ///

//         stage('SQuality Gate') 
//           {
//             steps 
//               {
//                 timeout(time: 5, unit: 'MINUTES') 
//                   {
//                      waitForQualityGate abortPipeline: true
//                   }
//               }
//           }

        stage('upload the artifacts on nexus') 
          {
            steps 
              {
                  nexusArtifactUploader artifacts: 
                  [
                    [
                      artifactId: 'LoginWebApp', 
                      classifier: '', 
                      file: '${Artifacts_path}', 
                      type: 'war'
                    ]

                  ], 
                      credentialsId: 'nexus-cred', 
                      groupId: 'com.devops4solutions', nexusUrl: '${Nexus_server_url}', 
                      nexusVersion: 'nexus3', 
                      protocol: 'http', 
                      repository: '${Nexus_repo_url}', 
                      version: "3"
              }
          }


        stage('Ansible Deploy') {

            steps {
                
                sh "ansible-playbook main.yml -i hosts "
        
            
            }
        }
        }
    
    }
