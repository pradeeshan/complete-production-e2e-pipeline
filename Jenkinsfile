pipeline{
    agent any

    tools {
        jdk 'jdk21'
        maven 'maven'
    }
    
    stages{
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }

        }
    
        stage("Checkout from SCM"){
            steps {
                git 'https://github.com/pradeeshan/complete-production-e2e-pipeline.git'
            }

        }

        stage("Build Application"){
            steps {
                bat "mvn clean package"
            }

        }

        stage("Test Application"){
            steps {
                bat "mvn test"
            }

        }
        
        stage("Sonarqube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        bat "mvn sonar:sonar"
                    }
                }
            }

        }

        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'

                }
            }

        }

    }

}
