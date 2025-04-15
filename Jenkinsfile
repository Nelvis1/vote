def registry = "484907489332.dkr.ecr.us-east-1.amazonaws.com"
def imagename = "def registry = "484907489332.dkr.ecr.us-east-1.amazonaws.com"
def imagename = "vote"
def region = "us-east-1"
def tag = ""

pipeline {
    agent any

    stages {
        stage("Init") {
            steps {
                script {
                    tag = getTag()
                    echo "Using image: ${registry}/${imagename}:${tag}"
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    sh "docker build -t ${registry}/${imagename}:${tag} ."
                }
            }
        }

        stage("Login to ECR") {
            steps {
                script {
                    withAWS(region: region, credentials: 'aws_creds') {
                        sh "aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${registry}"
                    }
                }
            }
        }

        stage("Push Docker Image") {
            steps {
                script {
                    withAWS(region: region, credentials: 'aws_creds') {
                        sh "docker push ${registry}/${imagename}:${tag}"
                    }
                }
            }
        }

        stage("Deploy to Dev") {
            when {
                branch 'develop'
            }
            steps {
                script {
                    withAWS(region: region, credentials: 'aws_creds') {
                        sh "aws eks update-kubeconfig --name vote-dev --region ${region}"
                        sh "kubectl get deployment vote -n vote || kubectl apply -f k8s/deployment.yaml -n vote"
                        sh "kubectl set image deployment/vote vote=${registry}/${imagename}:${tag} -n vote"
                        sh "kubectl rollout restart deployment/vote -n vote"
                    }
                }
            }
        }
    }
}

// Tag generator (timestamp-based)
def getTag() {
    return "build-${new Date().format('yyyyMMddHHmmss')}"
}"
def region = "us-east-1"
def tag = ""

pipeline {
    agent any

    stages {
        stage("Init") {
            steps {
                script {
                    tag = getTag()
                    echo "Using image: ${registry}/${imagename}:${tag}"
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    sh "docker build -t ${registry}/${imagename}:${tag} ."
                }
            }
        }

        stage("Login to ECR") {
            steps {
                script {
                    withAWS(region: region, credentials: 'aws_creds') {
                        sh "aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${registry}"
                    }
                }
            }
        }

        stage("Push Docker Image") {
            steps {
                script {
                    withAWS(region: region, credentials: 'aws_creds') {
                        sh "docker push ${registry}/${imagename}:${tag}"
                    }
                }
            }
        }

        stage("Deploy to Dev") {
            when {
                branch 'develop'
            }
            steps {
                script {
                    withAWS(region: region, credentials: 'aws_creds') {
                        sh "aws eks update-kubeconfig --name vote-dev --region ${region}"
                        sh "kubectl get deployment vote -n vote || kubectl apply -f k8s/deployment.yaml -n vote"
                        sh "kubectl set image deployment/vote vote=${registry}/${imagename}:${tag} -n vote"
                        sh "kubectl rollout restart deployment/vote -n vote"
                    }
                }
            }
        }
    }
}

// Tag generator (timestamp-based)
def getTag() {
    return "build-${new Date().format('yyyyMMddHHmmss')}"
}
