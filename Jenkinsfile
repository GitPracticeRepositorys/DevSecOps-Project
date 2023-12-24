pipeline {
    agent { label 'docker-node-1' }
    tools {
        jdk 'jdk11'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/GitPracticeRepositorys/DevSecOps-Project.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$sonar-scanner \
                             -Dsonar.projectKey=Netflix \
                             -Dsonar.sources=. \
                             -Dsonar.host.url=http://52.34.82.84:9000 \
                             -Dsonar.login=sqp_680280b7b0ba0e690a946f269a34a315ad4e075c'''
                }
            }
        }
        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
}
