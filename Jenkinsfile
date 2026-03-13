pipeline {
    agent any

    tools {
        nodejs "nodejs"
    }

    environment {
        SONAR_SCANNER_HOME = tool "sonar-scanner"
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

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                    $SONAR_SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=node-sonar-project \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://localhost:9000 \
                    -Dsonar.login=YOUR_SONAR_TOKEN
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t node-sonar-app .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 3000:3000 node-sonar-app'
            }
        }

    }
}
