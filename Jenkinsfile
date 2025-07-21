pipeline {
  agent any

  tools {
   maven 'maven'  // ou la version configurée dans Jenkins
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
