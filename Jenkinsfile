pipeline {
    agent any
    
     environment { 
        registry = "shashimls276/Assessment-ui"
        registryCredential = 'dockerhub_id' 
        dockerImage = ''
        PROJECT_ID = 'geometric-vim-314208'
        CLUSTER_NAME = 'assessment-cluster'
        LOCATION = 'asia-south1-a'
        CREDENTIALS_ID = 'gke' 
    }

    stages {
        
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = '/usr/lib/jvm/java-11-openjdk-amd64'"
                    echo "JAVA_HOME = /usr/lib/jvm/java-11-openjdk-amd64"
                    
                '''
            }
        }
        
        stage('Code Checkout') {
            steps {
                git 'https://github.com/devwork276/E-Assessment.git'
            }
        }
        
         stage('Code Build') {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage('Docker image Build'){
            steps {
                script{
                    //sh "docker image build -t demo ."
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        
        stage('Image push to Hub') { 

            steps { 
                script { 
                    docker.withRegistry( '', registryCredential ) { 
                        dockerImage.push() 
                         dockerImage.push("latest")
                    }
                } 
            }
        }
        
         stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/Assessment-ui:latest/Assessment-ui:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }  
        
    }
}
