pipeline {

    agent any

	environment {
		IMAGE_NAME = "asrorbekm/node${env.BRANCH_NAME}"
		REGISTRY = "https://index.docker.io/v1/"
		DOCKER_CREDS = "docker-hub-credentials"
		NODE_JS = 'NodeJS 7.8.0'
	}

    stages {
        stage("build") {
            steps {
                nodejs(nodeJSInstallationName: "${env.NODE_JS}") {
                    sh 'npm i'
                    sh 'npm run build'
                }
            }
        }

        stage("test") {
            steps {
                nodejs(nodeJSInstallationName: "${env.NODE_JS}") {
                    sh 'npm run test'
                }
            }
        }

        stage("dockerfile_lint") {
            steps {
                script{
                    def lintResults = sh(script: "hadolint Dockerfile", returnStdout: true).trim()

                    echo "Lint results:\n${lintResults}"
                }
            }
        }


		stage("build_image") {
		    environment {
		        LOGO_REMOTE_SRC = "${env.BRANCH_NAME == 'main' ? 'https://www.svgrepo.com/download/354259/react.svg' : 'https://www.svgrepo.com/download/374034/reacttemplate.svg'}"
		    }

            steps {
                script {
                    sh "wget -O src/logo.svg ${env.LOGO_REMOTE_SRC}"

                    def dockerImage = docker.build("${env.IMAGE_NAME}:v1")

                    docker.withRegistry("${env.REGISTRY}", "${env.DOCKER_CREDS}") {
                      dockerImage.push()
                    }
                }
            }
        }

        stage("Trivy Scan") {
            steps {
                script {
                   def vulnerabilities = sh(script: "trivy image --exit-code 0 --severity HIGH,MEDIUM,LOW --no-progress ${env.IMAGE_NAME}:v1", returnStdout: true).trim()

                   echo "Vulnerability Report:\n${vulnerabilities}"
                }
            }
        }

        stage("deploy") {
            environment {
                PORT = "${env.BRANCH_NAME == 'main' ? 3000 : 3001}"
                CONTAINER_NAME = "node${env.BRANCH_NAME}"
            }

            steps {
                script {
                    docker.withRegistry("${env.REGISTRY}", "${env.DOCKER_CREDS}") {
                        def dockerImage = docker.image("${env.IMAGE_NAME}:v1")
                        dockerImage.pull()
                        sh "docker rm -f ${env.CONTAINER_NAME} || true"
                        dockerImage.run("-p ${env.PORT}:3000 --name ${env.CONTAINER_NAME}")
                    }
                }
            }
        }

    }
}