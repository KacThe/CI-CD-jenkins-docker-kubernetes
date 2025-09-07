pipeline {
    agent any
    environment {
        USER = "kacthe"
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
        DEVOPS_DIR = "devops_config"
    }
    parameters {
        string(name: 'VERSION', defaultValue: "v${env.BUILD_NUMBER}", description: 'Version to build and deploy')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/KacThe/python-demo-app-devops.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${USER}/demo-app:${params.VERSION} ./${DEVOPS_DIR}"
            }
        }
        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login --username $DOCKER_USER --password-stdin'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                sh "docker push ${USER}/demo-app:${params.VERSION}"
            }
        }
        stage('Prepare manifest') {
            steps {
                sh "cp ${DEVOPS_DIR}/k8s_manifest.yaml manifest.deploy.yaml"
            }
        }
        stage('Patch image version') {
            steps {
                script {
                    def imageTag = "${env.USER}/demo-app:${params.VERSION}"
                    sh """
                        #!/bin/bash
                        sed -i 's|<IMAGE_TAG>|${imageTag}|g' manifest.deploy.yaml
                    """
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    #!/bin/bash
                    kubectl apply -f manifest.deploy.yaml
                '''
            }
        }
    }
}
