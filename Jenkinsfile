pipeline {
    agent any
    tools {
    	maven "Maven3"
    } 
    environment {
        registry = "288020836064.dkr.ecr.ap-south-1.amazonaws.com/zipkin"
    }
    stages {
        stage("Clone"){
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/shekar55/zipkin.git']])
           }
        }
	  stage('Build'){
		steps{
			sh "mvn clean package"
		}
	  }
        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build registry 
                    // docker.build('pet-clinic') 
                }
            }
        }
        stage('Pushing to ECR') {
            steps {
                script {
                    // sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 288020836064.dkr.ecr.ap-south-1.amazonaws.com'
                    // sh 'docker push 288020836064.dkr.ecr.ap-south-1.amazonaws.com/zipkin:latest'
                    docker.withRegistry('https://288020836064.dkr.ecr.ap-south-1.amazonaws.com/zipkin:latest', 'ecr:ap-ap-south-1:AWS') {
                        docker.image('288020836064.dkr.ecr.ap-south-1.amazonaws.com/zipkin').push('latest')
                    }
                }
                
            }
        }
        stage('K8S Deploy') {
            steps {
                withKubeConfig([credentialsId: 'K8S', serverUrl: '']) {
                    sh ('kubectl apply -f  zipkin.yaml')
                }
            }
        }
    }
}
