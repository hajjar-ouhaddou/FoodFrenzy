pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/hajjar-ouhaddou/FoodFrenzy.git',
                    credentialsId: 'github-token'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    bat 'mvn sonar:sonar -Dsonar.host.url=http://192.168.27.129:9000'
                }
            }
        }
    }

    post {
        success {
            echo '✔️ Build, tests, and SonarQube completed successfully.'
        }
        failure {
            echo '❌ Build or analysis failed.'
        }
    }
}
