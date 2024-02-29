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
                dependencyCheck additionalArguments: '--scan ./ --format XML ', odcInstallation: 'DP-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('Docker Image Build'){
			steps{
				script{
					sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
					sh 'docker image tag $JOB_NAME:v1.$BUILD_ID udayveersingh/$JOB_NAME:v1.$BUILD_ID'
					sh 'docker image tag $JOB_NAME:v1.$BUILD_ID udayveersingh/$JOB_NAME:latest'
				}
			}
		}

        stage('push image to the dockerhub'){
			steps{
				script{
					withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
                                            sh 'docker login -u udayveersingh -p${dockerhub}'
						sh'docker image push udayveersingh/$JOB_NAME:v1.$BUILD_ID'
						sh'docker image push udayveersingh/$JOB_NAME:latest'
                                      }
				}
			}
		}
        }
    }

