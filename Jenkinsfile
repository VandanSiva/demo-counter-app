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

    }
}
