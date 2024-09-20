node {
    def app
    stage('Clone repository') {
        git 'https://github.com/CloudComputing-SSWU/video-streaming.git'
    }
    stage('Build image') {
        app = docker.build("pjbear/video-streaming")
    }
    stage('Push image') {
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
           app.push("${env.BUILD_NUMBER}")
           app.push("latest")
        }
    }
}
