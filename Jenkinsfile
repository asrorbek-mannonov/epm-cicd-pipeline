pipeline {

    agent none

	environment {
		IMAGE_NAME = "asrorbekm/node${env.BRANCH_NAME}"
		DOCKER_HUB_CRED = credentials('docker-hub-credentials')
		HOME = "${env.WORKSPACE}"
	}


    stages {

         stage("build") {
             agent {
                 docker {
                     image "node:20-alpine"
                 }
             }
             steps {
                 sh 'npm i'
                 sh 'npm run build'
             }
         }

         stage("test") {
              agent {
                 docker {
                     image "node:20-alpine"
                 }
             }
             steps {
                 sh 'npm run test'
             }
         }


		stage("build_image") {
		    environment {
		        LOGO_REMOTE_SRC = "${env.BRANCH_NAME == 'main' ? 'https://www.svgrepo.com/download/354259/react.svg' : 'https://www.svgrepo.com/download/374034/reacttemplate.svg'}"
		    }
		    agent {
		        docker {
		            image "docker:27-dind"
		            args  '-v /var/run/docker.sock:/var/run/docker.sock:ro --group-add docker'
		        }
		    }

            steps {
                script {
                    sh "wget -O src/logo.svg ${env.LOGO_REMOTE_SRC}"

                    def dockerImage = docker.build("${env.IMAGE_NAME}:v1")

                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                       dockerImage.push()
                    }
                }
            }
        }

        stage("deploy") {
            environment {
                PORT = "${env.BRANCH_NAME == 'main' ? 3000 : 3001}"
            }
            agent {
		        docker {
		            image "docker:27-dind"
		            args  "-v /var/run/docker.sock:/var/run/docker.sock:ro --group-add docker"
		        }
		    }

            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        sh "docker run -p ${env.PORT}:3000 -d ${env.IMAGE_NAME}:v1 --name ${env.IMAGE_NAME}"
                    }
                }
            }
        }

    }
}