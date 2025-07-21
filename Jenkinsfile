pipeline {
  agent any

  tools {
    maven 'Maven 3.6.3'  // ou la version configurée dans Jenkins
  }

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/ton-utilisateur/foodfrenzy.git'
      }
    }

    stage('Build') {
      steps {
        sh 'mvn clean install -DskipTests'
      }
    }

    stage('Test') {
      steps {
        sh 'mvn test'
      }
    }
  }
}
