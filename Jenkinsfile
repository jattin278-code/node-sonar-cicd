pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh """
                    ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=node-sonar \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://sonarqube:9000
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t node-sonar-app .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker rm -f node-sonar-container || true'
                sh 'docker run -d -p 3000:3000 --name node-sonar-container node-sonar-app'
            }
        }

    }
}
