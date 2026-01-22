pipeline {
    agent any

    environment {
        DOCKER_REPO = "mpradeepdeepu/flask-ci-cd-demo"
        INVENTORY = "inventory"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/mukkamallapradeep/python_ci_cd_ansible.git'
            }
        }

        stage('Build Flask Docker Image') {
            steps {
                sh """
                    docker build -t $DOCKER_REPO:${BUILD_NUMBER} .
                    docker tag $DOCKER_REPO:${BUILD_NUMBER} $DOCKER_REPO:latest
                """
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'docker-pass', variable: 'PASS')]) {
                    sh """
                        echo "$PASS" | docker login -u your-dockerhub-username --password-stdin
                        docker push $DOCKER_REPO:${BUILD_NUMBER}
                        docker push $DOCKER_REPO:latest
                    """
                }
            }
        }

        stage('Deploy using Ansible') {
            steps {
                sh """
                    ansible-playbook -i ${INVENTORY} ansible/deploy.yml \
                    --extra-vars "image_tag=$DOCKER_REPO:${BUILD_NUMBER}"
                """
            }
        }
    }
}
