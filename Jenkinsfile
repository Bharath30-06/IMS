pipeline {
    agent any
    parameters {
        string(name: 'VERSION', description: 'Enter the app version')
    }
    environment {
        AWS_ACCOUNT_ID = "820242918451"
        REGION = "ap-south-1"
        REPO_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com/coursevitas"
        DOCKER_REGISTRY = "docker.io"
        DOCKER_REGISTRY_CRED = 'docker_cred'  // Updated to match global credentials
    }
    stages {
        stage('Git Clone') {
            steps {
                echo "Cloning the repo"
                git url: 'https://github.com/Bharath30-06/IMS.git', branch: 'sragvi'
            }
        }

        stage('Docker Build') {
            steps {
                dir("${env.WORKSPACE}") {
                    sh "docker build -t ims3:${VERSION} ."
                }
            }
        }

        stage('Image push to ECR') {
            steps {
                script {
                    withAWS(credentials: 'aws_cred', region: "${REGION}") {  // Updated to match global credentials
                        sh """
                            aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${REPO_URI}
                            docker tag ims3:${VERSION} ${REPO_URI}:${VERSION}
                            docker push ${REPO_URI}:${VERSION}
                        """
                    }
                }
            }
        }

        stage('Image push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CRED}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {  // Updated to match global credentials
                        sh """
                            docker login -u $USERNAME -p $PASSWORD
                            docker tag ims3:${VERSION} bharathsunny3006/ims:${VERSION}
                            docker push bharathsunny3006/ims:${VERSION}
                        """
                    }
                }
            }
        }
