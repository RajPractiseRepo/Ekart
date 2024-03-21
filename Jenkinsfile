pipeline {
    agent any 
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    
    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', url: 'https://github.com/RajPractiseRepo/Ekart.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
    
        
           stage('Sonarqube Analysis') {
            steps {
                sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.url=http://54.173.88.192:9000/ \
                    -Dsonar.login=squ_bd40955fce00d6d7025290ce09b282ddec552f45 \
                    -Dsonar.projectName=Shopping-Cart \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Shopping-Cart
                '''
            }
        }
        
           stage("Build"){
            steps{
                sh " mvn clean package -DskipTests=true"
            }
        }
        
        stage("OWASP Dependency check"){
            steps{
               dependencyCheck additionalArguments: '--scan target/', odcInstallation: 'owasp'
                  dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
         stage("Build Docker Image"){
            steps{
               script{
                   
                   withDockerRegistry(credentialsId: 'ae5ff369-865d-4e4f-b6a7-98526ea1007a', toolName: 'docker') {
                   
                   sh "docker build -t shopping-cart -f docker/Dockerfile ."
                   sh "docker tag shopping-cart rajpractise/shopping-cart:latest"
    
                }
                   
               }
            }
        }
        
        
        stage("Push Docker Image"){
            steps{
               script{
                   
                   withDockerRegistry(credentialsId: 'ae5ff369-865d-4e4f-b6a7-98526ea1007a', toolName: 'docker') {
                   
                   sh "docker push rajpractise/shopping-cart:latest"
    
                }
                   
               }
            }
        }
        
        
         stage("Deploy to Docker Container"){
            steps{
               script{
                   
                   withDockerRegistry(credentialsId: 'ae5ff369-865d-4e4f-b6a7-98526ea1007a', toolName: 'docker') {
                   
                   sh "docker run -d --name shopping -p 8070:8070  rajpractise/shopping-cart:latest"
    
                }
                   
               }
            }
        }
        
        
        
        
    }
}
