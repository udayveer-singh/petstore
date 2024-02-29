pipeline {
    agent any
       
       tools {
           jdk 'jdk17'
           maven 'maven3'
       }
     
     
    stages {
        stage('Workspace clean') {
            steps {
                cleanWs()
            }
        }
        
        stage('Git checkout') {
            
            steps {
                git branch: 'main', url: 'https://github.com/udayveer-singh/petstore.git'
            }
        }
        
        
        stage('maven complie') {
            
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('maven Test') {
            
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Sonarqube Analysis') {
            
            steps {
                
                withSonarQubeEnv('sonar-server') {
                    sh 'mvn clean package sonar:sonar'
                }
            }
        }
        stage('Quality Gate Analysis') {
            
            steps {
                
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api'
                }
            }
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --format XML ', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        }
    }

