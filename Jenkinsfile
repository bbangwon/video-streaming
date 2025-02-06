pipeline {
    agent any
    environment {
        PROJECT_ID = 'inspired-arena-447810-n2'
        CLUSTER_NAME = 'k8s'
        LOCATION = 'asia-northeast3'
        CREDENTIALS_ID = 'gke'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("cyi38317/video-streaming:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to GKE') {
			when {
				branch 'main'
			}
            steps{
                sh "sed -i 's/video-streaming:latest/video-streaming:${env.BUILD_ID}/g' video-streaming.yml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'video-streaming.yml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: false])
            }
        }
    }    
}
