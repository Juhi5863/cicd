pipeline {
    agent any

    environment {
        REGISTRY = 'docker.io/juhichoudhary'
        IMAGE_NAME = 'java-microservice'
        KUBE_CONFIG = credentials('kubeconfig') // Secret file in Jenkins for your kubeconfig
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
