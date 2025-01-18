pipeline {
    agent any
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/jaiswaladi246/Ekart.git'
            }
        }
        stage('Compile') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        
        stage('OWASPScan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        stage('Docker Build & Push'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-creds', toolName: 'docker') {
                        // some block
                        sh "docker build -t shopping-cart -f docker/Dockerfile ."
                        sh "docker tag shopping-cart briarheart1096/shopping-cart:latest"
                        sh "docker push briarheart1096/shopping-cart:latest"
                    }
                }
            }
        }
        
        stage('Docker deploy'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-creds', toolName: 'docker') {
                        // some block
                        sh "docker run -d --name shop-shop -p 8070:8070 briarheart1096/shopping-cart:latest"
                    }
                }
            }
        }
        
    }
}
