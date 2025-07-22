pipeline {
    agent any

    tools {
        maven 'maven' // Assure-toi que le Maven est bien configuré dans Jenkins (Manage Jenkins > Global Tool Configuration)
    }

    environment {
        DOCKER_IMAGE = 'hajjarouhaddou/foodfrenzy' // Change ce nom si ton repo DockerHub est différent
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/hajjar-ouhaddou/FoodFrenzy.git', credentialsId: 'github-token'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=foodfrenzy -Dsonar.host.url=http://192.168.27.129:9000'
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-id', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh """
                        echo "$PASSWORD" | docker login -u "$USERNAME" --password-stdin
                        docker build -t $DOCKER_IMAGE .
                        docker push $DOCKER_IMAGE
                    """
                }
            }
        }
    }

    post {
        failure {
            echo '❌ Build or analysis failed.'
        }
        success {
            echo '✅ Build, test, analysis, and Docker push succeeded!'
        }
    }
}
