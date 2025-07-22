pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        DOCKER_IMAGE = 'hajjarouhaddou/foodfrenzy'
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
        success {
            echo '✅ Build, tests, SonarQube analysis, and Docker push completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs above.'
        }
    }
}
