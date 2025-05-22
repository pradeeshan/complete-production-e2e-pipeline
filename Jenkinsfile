pipeline{
    agent any

    tools {
        jdk 'jdk21'
        maven 'maven'
    }

    environment {
        DOCKER_USER = "pradeeshan"
        IMAGE_NAME = 'myapp'
        IMAGE_TAG = 'latest'
        FULL_IMAGE_NAME = "${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
        
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

        stage("Build Docker Image") {
            steps {
                script {
                    bat "docker build -t ${FULL_IMAGE_NAME} ."
                }
            }
        }

        stage("Push to Docker Hub") {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker') {
                        bat "docker push ${FULL_IMAGE_NAME}"
                    }
                }
            }
        }

        stage("Deploy Container") {
            steps {
                script {
                    
                    bat """
                        docker rm -f ${IMAGE_NAME} || true
                        docker run -d --name ${IMAGE_NAME} -p 3000:3000 ${FULL_IMAGE_NAME}
                    """
                }
            }
        }


    }

}
