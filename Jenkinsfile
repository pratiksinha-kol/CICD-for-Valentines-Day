pipeline {
    agent any
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/pratiksinha-kol/CICD-for-Valentines-Day.git'
            }
        }
    
    
    
        stage('Trivy FileSystem Scan') {
            steps {
                echo 'trivy fs --format table -o trivy-fs-report.html .'
            }
        }
    
    
    
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Valentine -Dsonar.projectName=Valentine'
                }
            }
        }
    
    
    
        stage('Build and tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker build -t pratiksinha20/valentine:v1 .'
                    }
                }
            }
        }
        
    
    
        stage('Trivy Docker Image Scan') {
            steps {
                sh 'trivy image --format table -o docker-image-trivy-report.html pratiksinha20/valentine:v1'
            }
        }
    
    
    
        stage('Docker Image Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker push pratiksinha20/valentine:v1'
                    }
                }
            }
        }
    
    
    
        stage('Deply Docker Image & Create Application') {
            steps {
                sh 'docker run -d -p 8081:80 pratiksinha20/valentine:v1'
            }
        }
    }    
}