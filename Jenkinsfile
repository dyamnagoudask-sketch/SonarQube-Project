pipeline {
    agent any

    tools {
        maven 'maven3'
        jdk 'jdk17'
        // Remove the sonarQube line if it still causes issues
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKERHUB_USERNAME = 'akasha123' 
        DOCKER_IMAGE = "${DOCKERHUB_USERNAME}/spotify-app:latest"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Pramod-project/SonarQube-Project-Kastro.git'
            }
        }

        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }

        stage('Test') {
            steps {
                sh "mvn test"
            }
        }

        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Pramod -Dsonar.projectKey=PramodKey -Dsonar.java.binaries=target"
                }
            }
        }

        stage('Build') {
            steps {
                sh "mvn package"
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    // Building Docker Image
                    sh "docker build -t $DOCKER_IMAGE ."
                }
            }
        }

        stage('Docker Push to DockerHub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', 
                                  usernameVariable: 'DOCKERHUB_USERNAME', 
                                  passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                           sh """
                                  docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD
                                  docker build -t $DOCKER_IMAGE .
                                  docker push $DOCKER_IMAGE
                              """
                    }
                }
            }
        }


        stage('Run Docker Container') {
            steps {
                script {
                    // Stop any existing container with the same name
                    sh "docker stop spotify-app || true && docker rm spotify-app || true"
                    
                    // Running the container
                    sh "docker run -d --name spotify-app -p 5555:5555 $DOCKER_IMAGE"
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()  // Clean up the workspace after the build
        }
    }
}







Explain Each Stage in Simple & Strong Terms
⭐ Git Checkout

“The pipeline pulls the latest code from the main branch to ensure we always work with the most updated application version.”

⭐ Compilation

“We compile the source code using Maven to validate that the project builds successfully.”

⭐ Unit Tests

“Tests are executed to maintain code stability and verify that recent changes haven’t broken existing functionality.”

⭐ SonarQube Analysis

“The code is scanned with SonarQube for static code analysis—this checks code smells, bugs, vulnerabilities, and maintains code quality metrics.”

⭐ Build Artifact

“Maven packages the application into a deployable JAR file.”

⭐ Docker Build

“We containerize the application using Docker, ensuring consistency across environments.”

⭐ DockerHub Push

“The built image is pushed to DockerHub for centralized storage and easy deployment across environments.”

⭐ Deployment

“We run the latest Docker image as a container, making the application live.”



