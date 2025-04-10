pipeline {
    agent any

    environment {
        REGISTRY = 'docker.io/juhichoudhary'
        IMAGE_NAME = 'java-microservice'
        KUBE_CONFIG = credentials('kubeconfig')
    }

    options {
        skipStagesAfterUnstable()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Docker Build & Push') {
            when {
                branch 'develop'
            }
            steps {
                script {
                    def imageTag = "${env.BUILD_NUMBER}"
                    sh """
                        docker build -t $REGISTRY/$IMAGE_NAME:$imageTag .
                        echo "$DOCKERHUB_PASSWORD" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin
                        docker push $REGISTRY/$IMAGE_NAME:$imageTag
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                branch 'develop'
            }
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    script {
                        def imageTag = "${env.BUILD_NUMBER}"
                        sh """
                            sed 's|<IMAGE>|$REGISTRY/$IMAGE_NAME:$imageTag|' k8s/deployment.yaml | kubectl apply -f -
                            kubectl apply -f k8s/service.yaml
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }
        failure {
            echo 'Build failed.'
        }
    }
}
