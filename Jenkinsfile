pipeline {
    agent any
    triggers {
    githubPush()
  }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('Dockerhub')
        }

    stages {
        stage('Verify Branch') {
            steps {
                echo "$GIT_BRANCH"
            }
        }

        stage('Login to Dockerhub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Pulling base image from Dockerhub') {
            steps {
                    sh 'docker pull gradproj/amf-base'
            }
        }

        stage('docker build') {
            steps {
                sh(script: """
                    docker images -a
                    docker build -t gradproj/5g-amf:latest . 
                    docker images -a
                """)
            }
        }

        stage('Scan Image for Common Vulnerabilities and Exposures') {
            steps {
                sh 'trivy image gradproj/5g-amf --output trivy-report.json'
            }
        }
        stage('Pushing to Dockerhub') {
            steps {
                sh 'docker push gradproj/5g-amf:latest'
            }
        }

        stage('Build and Package Helm Chart') {
            steps {
                sh 'helm package ./helm/'
            }
        }

        stage('Configure Kubernetes Context') {
            steps {
                sh 'aws eks --region us-east-1 update-kubeconfig --name 9G-Core-Net'
            }
        }

        stage('Deploy Helm Chart on EKS') {
            steps {
                sh 'helm upgrade --install amf ./helm/'
            }
        }
    }

    post {
        always {
            // Archiving Test Result
            archiveArtifacts artifacts: 'trivy-report.json', fingerprint: true
            sh 'docker rmi gradproj/5g-amf'
            sh 'docker rmi gradproj/amf-base'
        }
    }
}





