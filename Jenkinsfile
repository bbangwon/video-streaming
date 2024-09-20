pipeline {
    agent any
    environment {
        PROJECT_ID = 'k-mooc-427802'
        CLUSTER_NAME = 'gke_k-mooc-427802_asia-northeast3_k8s'
        LOCATION = 'asia-northeast3-a'
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
                    myapp = docker.build("pjbear/video-streaming:${env.BUILD_ID}")
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
