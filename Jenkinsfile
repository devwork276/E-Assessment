pipeline {
    agent any
    
     environment { 
        registry = "shashimls276/eassessment-ui"
        registryCredential = 'dockerhub_id' 
        dockerImage = ''
        PROJECT_ID = 'mystic-airway-335416'
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
                git 'https://github.com/devwork276/e-assessment.git'
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
                sh "sed -i 's/eassessment-ui:latest/eassessment-ui:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }  
        
    }
}
