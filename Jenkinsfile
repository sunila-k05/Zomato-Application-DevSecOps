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
        sh '''
        docker run --rm \
          -v /var/lib/docker/volumes/jenkins_home/_data/workspace/DevSecOps-Pipeline:/app \
          -w /app \
          node:18 \
          npm install
        '''
    }
}


stage('OWASP Dependency Check') {
    steps {
        script {
            sh '''
            docker run --rm \
              -v /var/lib/docker/volumes/jenkins_home/_data/workspace/DevSecOps-Pipeline:/src \
              -v dependency-check-data:/usr/share/dependency-check/data \
              owasp/dependency-check:latest \
              --project "Zomato-App" \
              --scan /src \
              --format HTML \
              --out /src/dependency-check-report \
              --failOnCVSS 11
            '''
        }
        archiveArtifacts artifacts: 'dependency-check-report/dependency-check-report.html', fingerprint: true
    }
}



stage('SonarCloud Analysis') {
    environment {
        SONAR_TOKEN = credentials('sonarcloud-token')
    }
    steps {
        script {
            def scannerHome = tool 'sonar-scanner'
            sh """
            ${scannerHome}/bin/sonar-scanner \
              -Dsonar.projectKey=sunila-k05_Zomato-Application-Secure-Deployment-with-Dev-Sec-Ops-CI-CD \
              -Dsonar.organization=sunila-k05 \
              -Dsonar.host.url=https://sonarcloud.io \
              -Dsonar.sources=src \
              -Dsonar.exclusions=node_modules/** \
              -Dsonar.c.file.suffixes=- \
              -Dsonar.cpp.file.suffixes=- \
              -Dsonar.objc.file.suffixes=- \
              -Dsonar.token=${SONAR_TOKEN}
            """
        }
    }
}


stage('Build Docker Image') {
    steps {
        sh '''
        docker build -t sunilak05/zomato-app:latest .
        '''
    }
}
stage('Trivy FS Scan') {
    steps {
        sh '''
        docker run --rm -v $PWD:/app aquasec/trivy fs /app
        '''
    }
}



stage('Push to Docker Hub') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'docker-credentials',
                         usernameVariable: 'DOCKER_USER',
                         passwordVariable: 'DOCKER_PASS')]) {
            sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            docker push sunilak05/zomato-app:latest
            '''
        }
    }
}


stage('Deploy Container') {
    steps {
        sh '''
        docker stop zomato-app || true
        docker rm zomato-app || true
        docker run -d -p 3000:3000 --name zomato-app \
        sunila-k05/zomato-app:latest
        '''
    }
}

 }
}
