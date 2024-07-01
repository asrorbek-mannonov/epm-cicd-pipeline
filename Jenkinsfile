pipeline {

    agent {
        docker {
            image "alpine"
        }
    }

    tools {
        nodejs "Node 7.8.0"
    }

	environment {
		IMAGE_NAME = 'epm-cicd-nodejs'
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
								sh 'printenv | sort'
                sh 'npm run test'
            }
        }


				stage("build_image") {
            steps {
                script {
                    docker.image('node:20').inside('docker') {
                        sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
                    }
                }
            }
        }

        stage("deploy") {
            steps {
                script {
                    docker.image($IMAGE_NAME + ":" + $BUILD_NUMBER).withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
                    }
                }
            }
        }

    }
}
