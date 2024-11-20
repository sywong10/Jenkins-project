pipeline {
    agent any
    environment {
        IMAGE_NAME = 'sanjeevkt720/jenkins-flask-app'
        IMAGE_TAG = "${IMAGE_NAME}:${env.BUILD_NUMBER}"
//         KUBECONFIG = credentials('kubeconfig-credentials-id')
// test, let's do it again, one more time

    }
    stages {

        stage('Checkout') {
            steps {
                dir('/Users/sallywong/Documents/Sally/classes/kodekloud/jenkins/out/')
                    git url: 'https://github.com/sywong10/Jenkins-project', branch: 'main'
                    sh "ls -ltr"
            }
        }
        stage('Setup') {
            steps {
                sh """
                export PATH=$PATH:/Users/sallywong/Documents/Sally/classes/kodekloud/jenkins/jenkins/bin
                echo $PATH
                cd /Users/sallywong/Documents/Sally/classes/kodekloud/jenkins
                python3 -m venv jenkins2
                source jenkins2/bin/activate
                which python
                which pip
                pip --version
                pip install -r requirements.txt
                """
            }
        }
        stage('Test') {
            steps {
                sh "pytest"
                sh "whoami"
            }
        }
        stage('Login to docker hub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                sh 'echo ${dockerhubpwd} | docker login -u sanjeevkt720 --password-stdin'}
                echo 'Login successfully'
            }
        }
        stage('Build Docker Image')
        {
            steps
            {
                sh 'docker build -t ${IMAGE_TAG} .'
                echo "Docker image build successfully"
                sh "docker images"
            }
        }
        stage('Push Docker Image')
        {
            steps
            {
                sh 'docker push ${IMAGE_TAG}'
                echo "Docker image push successfully"
            }
        }
        stage('Deploy to EKS Cluster') {
            steps {
                sh "kubectl apply -f deployment.yaml"
                echo "Deployed to EKS Cluster"
            }
        }
    }
}