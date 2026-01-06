pipeline {
    agent any

    tools {
        maven 'maven3'
        jdk 'jdk17'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        SONAR_HOST_URL = 'http://18.191.186.152:9000'
        DOCKER_IMAGE = 'akasha123/spotify-app:latest'
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/dyamnagoudask-sketch/SonarQube-Project.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh """
                    ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectName=Dyamanagouda \
                    -Dsonar.projectKey=DyamanagoudaKey \
                    -Dsonar.java.binaries=target \
                    -Dsonar.host.url=${SONAR_HOST_URL} \
                    -Dsonar.login=${SONAR_TOKEN}
                    """
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Docker Push to DockerHub') {
            steps {
                withDockerRegistry(
                    credentialsId: 'dockerhub-credentials',
                    url: 'https://index.docker.io/v1/'
                ) {
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker stop spotify-app || true
                docker rm spotify-app || true
                docker run -d --name spotify-app -p 5555:5555 ${DOCKER_IMAGE}
                '''
            }
        }
    }

    post {
        always {
            cleanWs()
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





