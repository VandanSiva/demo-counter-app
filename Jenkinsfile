pipeline {

    agent any

    stages{

        stage('Git Checkout'){

            steps{
                git branch: 'main', url: 'https://github.com/VandanSiva/demo-counter-app.git'
            }
        }

        stage('Unit Testing'){
            
            steps{
                sh 'mvn test'
            }
        }
        stage('Integration Test'){
            
            steps{
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage('Mvaen Building'){
            
            steps{
                sh 'mvn clean install'
            }
        }
        stage('Static code Analysis'){

            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-api') {
                        sh 'mvn clean package sonar:sonar'               
                    }
                }
            }
        }
        stage('Quality gate status'){

            steps{

                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api'
                }
            }
        }
        stage('upload war file to nexus'){

            steps{

                script{

                    def readPomVersion = readMavenPom file: 'pom.xml'
                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot',
                            classifier: '', file: 'target/Uber.jar',
                            type: 'jar'
                        ]
                    ], 
                    credentialsId: 'nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '34.238.27.179:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepo, 
                    version: "${readPomVersion.version}"                  

                }
            }
        }
        stage('Docker image building'){

            steps{

                script{

                    sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID 150419/$JOB_NAME:v1.$BUILD_ID'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID 150419/$JOB_NAME:latest'
                }
            }
        }
        stage('Push image to dockerhub'){

            steps{
                
                script{

                    withCredentials([string(credentialsId: 'git_creds', variable: 'dockerhub_cred')]) 
                    {
                        sh 'docker login -u 150419 -p ${dockerhub_cred}'
                        sh 'docker image push 150419/$JOB_NAME:v1.$BUILD_ID'
                        sh 'docker image push 150419/$JOB_NAME:latest'
                   
                    }
                }
            }
        }

    }
}
