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

        
    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                          usernameVariable: 'DOCKERHUB_USER',
                                          passwordVariable: 'DOCKERHUB_PASS')]) {
          sh '''#!/usr/bin/env bash
            set -euo pipefail
    
            # Always logout to avoid mixing sessions in shared agents
            docker logout || true
    
            # Login securely via stdin; expansion is done by the shell, not Groovy
            echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
    
            docker push "$DOCKER_REPO:${BUILD_NUMBER}"
            docker push "$DOCKER_REPO:latest"
    
            docker logout
          '''
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
