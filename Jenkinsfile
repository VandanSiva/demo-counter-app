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
    }
}
