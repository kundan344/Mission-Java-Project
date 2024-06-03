pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment {
        
        SCANNER_HOME= tool "sonar-scanner"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/kundan344/Mission-Java-Project.git'
            }
        }
        
        stage('Code Compile') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('Code Test') {
            steps {
                sh "mvn test -DskipTests=true"
            }
        }
        
        stage('Trivy-fs Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        
        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=mission -Dsonar.projectKey=mission \
                            -Dsonar.java.binaries=. '''
                }
            }
        }
        stage('Code Build') {
            steps {
                sh "mvn package -DskipTests=true"
            }
        }
        
        stage('Build Docker Image & Push Docker Hub') {
            steps {
                script {
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                     sh "docker build -t kundankumar344/mission:latest ."
                     sh "docker push kundankumar344/mission:latest"
} 
                }
 
                
            }
        }
        
        stage('deloy') {
            steps {
                sh "docker kill mission-app"
                sh "docker run -d --name misson-app -p 8082:8080 kundankumar344/mission:latest"
                sh "docker ps"
            }
        }
        
        
    }
}
