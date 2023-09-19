pipeline {
    agent any
    tools{
        jdk  'jdk11'
        maven  'maven3'
    }
    environment {
        
        SCANNER_HOME= tool 'sonar-scanner'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: 'aa25114d-9fbc-4212-ad15-50c4c96dfc60', poll: false, url: 'https://github.com/atinmondal/Shopping-Cart.git'
            }
        }    
        stage('Compile') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        stage('Sonarqube Analysis') {
            steps {
              withSonarQubeEnv('sonar-server'){
                  sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Shopping-Cart \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Shopping-Cart ''' 
              }
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        stage('Docker build & push') {
            steps {
                script{
                    // This step should not normally be used in your script. Consult the inline help for details.
                    withDockerRegistry(credentialsId: '3ae14504-150f-4b67-a930-0e75d05c715b', toolName: 'docker') {
                       
                       sh "docker build -t shopping-cart -f docker/Dockerfile ."
                       sh "docker tag shopping-cart atinspandan/shopping-cart:latest"
                       sh "docker push atinspandan/shopping-cart:latest"
                    }
                }
            }
        }
        
    } 
    
}
