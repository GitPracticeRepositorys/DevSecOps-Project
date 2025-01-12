pipeline{
    agent { label 'docker-node-1' }
    tools{
        jdk 'jdk11'
        nodejs 'nodejs'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/GitPracticeRepositorys/DevSecOps-Project.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage("Docker Build & Push"){
            steps{ 
                sh "docker tag netflix shivakrishna99/netflix:latest "
                sh "docker push shivakrishna99/netflix:latest "
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image shivakrishna99/netflix:latest > trivyimage.txt" 
            }
        }
        stage('Deploy to container'){
            steps{
                sh 'docker run -d --name netflix -p 8081:80 shivakrishna99/netflix:latest'
            }
        }
        stage('Deploy to kubernets'){
            steps{
                dir('Kubernetes') {
                sh 'kubectl apply -f deployment.yml'
                sh 'kubectl apply -f service.yml'
                }
            }
        }
    }
}
