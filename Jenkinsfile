pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            environment{
                DOCK_CREDS = credentials('dock-cred')
            }
            steps {
                echo 'Image building started....'
                sh("docker login -u ${DOCK_CREDS} -p ${DOCK_CREDS}")
                sh('sudo -i')
                sh('cd /')
                sh('cd /home/somesh/dockSomesh')
                sh('docker build -t someshlad/jen-image .')
            }
        }
        stage('Publish to Hub/Registry') {
            steps {  
                echo "Connected to Docker-Hub"
                echo 'Publishing the image to Docker-Hub...'
                sh"""
                sudo -i
                docker image push --all-tags someshlad/jen-image
                """
                echo '...Image successfully pushed'
            }
        }
        stage('pull image from hub/registry') {
            steps {
                echo 'Pulling the image from docker-hub...'
                sh('docker pull someshlad/jen-image')
                echo '...Image successfully pulled'
                sh('docker images')
            }
        }
        stage('Starting the container') {
            steps {
               sh '''
               if [ $(docker ps | awk \'{print $NF}\' | grep somesh-jen-container) = \'somesh-jen-container\' ]; then
                        docker stop "somesh-jen-container"
                        docker rm "somesh-jen-container"
                fi
                '''
                sh('docker run -it -d -p 8081:80 --name somesh-jen-container someshlad/jen-image')
                sh('docker exec somesh-jen-container service nginx start')
            }
        }
    }
}