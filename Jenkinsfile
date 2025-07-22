pipeline {
    agent any

    tools {
        maven 'maven' // doit correspondre au nom configuré dans Jenkins (tu l'as bien fait)
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-id')  // à configurer dans Jenkins (ID secret DockerHub)
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/hajjar-ouhaddou/FoodFrenzy.git',
                    credentialsId: 'github-token' // tu l'as déjà configuré
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

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t hajjarouhaddou/foodfrenzy-app .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-id', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        echo "$PASSWORD" | docker login -u "$USERNAME" --password-stdin
                        docker push hajjarouhaddou/foodfrenzy-app
                    '''
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                sh '''
                    docker stop foodfrenzy || true
                    docker rm foodfrenzy || true
                    docker run -d --name foodfrenzy -p 8080:8080 hajjarouhaddou/foodfrenzy-app
                '''
            }
        }
    }

    post {
        failure {
            echo '❌ Échec du pipeline.'
        }
        success {
            echo '✅ Pipeline terminé avec succès.'
        }
    }
}
