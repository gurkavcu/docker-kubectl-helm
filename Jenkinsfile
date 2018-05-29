podTemplate(label: 'kubectl-builder',
            containers: [                  
                    containerTemplate(name: 'docker', image: 'docker/compose:1.21.0', command: 'cat', ttyEnabled: true)
            ],
            volumes: [
                    hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
            ]) {

        node('kubectl-builder') {
            
            stage('Checkout') {
              checkout scm
            }     

            stage('Build docker images') {
                def GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                container('docker') {
                    def SERVICE_NAME = "kubectl-helm"
                    def DOCKER_IMAGE_REPO = "304703668734.dkr.ecr.eu-central-1.amazonaws.com/kubectl-helm"


                    withDockerRegistry([credentialsId: 'ecr:eu-central-1:aws-cred', url: "https://${DOCKER_IMAGE_REPO}"]) {
                        sh """
                           docker-compose up --build
                           docker tag kubectl-helm:2.8.2 ${DOCKER_IMAGE_REPO}:2.8.2
                           docker tag kubectl-helm:2.9.1 ${DOCKER_IMAGE_REPO}:2.9.1                           
                           docker tag kubectl-helm:2.9.1 ${DOCKER_IMAGE_REPO}:latest
                           docker push ${DOCKER_IMAGE_REPO}:2.8.2  
                           docker push ${DOCKER_IMAGE_REPO}:2.9.1                           
                           docker push ${DOCKER_IMAGE_REPO}:latest                           
                           """ 
                    }
                }
            }
        }
    }