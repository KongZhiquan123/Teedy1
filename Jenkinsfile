pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub_credentials')
        DOCKER_IMAGE = 'kongzhiquan/teedy'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/KongZhiquan123/Teedy1.git', branch: 'master'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Build Image') {
            steps {
                script {
                    docker.build("${env.DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                }
            }
        }
        stage('Upload Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', env.DOCKER_HUB_CREDENTIALS) {
                        def img = docker.image("${env.DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                        img.push()
                        img.push('latest')
                    }
                }
            }
        }
        stage('Run Container') {
            steps {
                script {
                    sh 'docker stop teedy-container-8081 || true'
                    sh 'docker rm teedy-container-8081 || true'
                    docker.image("${env.DOCKER_IMAGE}:${env.BUILD_NUMBER}").run('--name teedy-container-8081 -d -p 8081:8080')
                    sh 'docker ps --filter "name=teedy-container"'
                }
            }
        }
    }
}
