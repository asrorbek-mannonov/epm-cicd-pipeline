pipeline {

    agent any

    stages {

        stage("build") {
            steps {
                echo "Building the application"
            }
        }

        stage("test") {
            steps {
                echo "Testing the application"
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