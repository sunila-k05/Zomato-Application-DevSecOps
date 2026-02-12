pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                echo "Cloning repository..."
                checkout scm
            }
        }

        stage('Verify') {
            steps {
                echo "Repository cloned successfully!"
                sh 'ls -la'
            }
        }
stage('Install Dependencies') {
    steps {
        echo "Installing dependencies..."
        sh 'npm install'
    }
}


stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('sonarqube-server') {
            sh 'sonar-scanner'
        }
    }
}



 }
}
