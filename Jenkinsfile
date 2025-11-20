pipeline {
    agent any
    tools {
        maven 'Maven3'
    }

    environment {
        GIT_HOME   = "C:\\Program Files\\Git\\bin"
        DOCKER_BIN = "C:\\Program Files\\Docker\\Docker\\resources\\bin"
        JAVA_HOME  = "C:\\Program Files\\Java\\jdk-21"

        PATH = "${env.GIT_HOME};${env.DOCKER_BIN};${env.PATH}"

        SONARQUBE_SERVER = "SonarQubeServer"
        SONAR_TOKEN = credentials('sqa_14bbc7fa82bb011c04e5dcda04c832c7ab6a245d')  // safer than hardcoding!

        DOCKERHUB_CREDENTIALS_ID = "Docker_Hub"
        DOCKERHUB_REPO = "amirdirin/week5_students_emmi2"
        DOCKER_IMAGE_TAG = "latest"
    }


    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ADirin/students_emmi2.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
                    steps {
                        withSonarQubeEnv('SonarQubeServer') {
                            bat """
                                mvn sonar:sonar ^
                                -Dsonar.projectKey=devops-demo ^
                                -Dsonar.projectName=DevOps-Demo ^
                                -Dsonar.host.url=http://localhost:9000 ^
                                -Dsonar.login=${env.SONAR_TOKEN}

                            """
                        }
                    }
                }



        stage('Build Docker Image') {
                    steps {
                        script {
                            docker.build("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}")
                            // Or specify Dockerfile path explicitly if needed
                            // docker.build("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}", "-f ./Dockerfile .")
                        }
                    }
                }

                stage('Push Docker Image to Docker Hub') {
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS_ID) {
                                docker.image("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}").push()
                            }
                        }
                    }
                }
    }
}
