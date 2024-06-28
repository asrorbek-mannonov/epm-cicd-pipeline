pipeline {

    agent any

    tools {
        nodejs "Node 7.8.0"
    }

    stages {

        stage("build") {
            steps {
                sh 'npm i'
                sh 'npm run build'
            }
        }

        stage("test") {
            steps {
                sh 'npm run test'
            }
        }

        stage("build_image") {
            steps {
                echo "Building docker image the application"
            }
        }

        stage("deploy") {
            steps {
                echo "Publishing docker image to Docker Hub"
            }
        }

    }
}