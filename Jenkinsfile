pipeline {
    agent any

    environment {
        HELM_CHART_REPO = "https://github.com/TAM360/solace-jenkins"
        HELM_RELEASE_NAME = "pubsubplus"
        HELM_NAMESPACE = "default"
        KUBE_CONFIG = credentials('kubeconfig-id') // Store kubeconfig in Jenkins credentials as text
    }

    stages {
        stage('Checkout') {
          steps {
              git branch: 'main', credentialsId: 'github-pat', url: "${HELM_CHART_REPO}"
          }
        }

        stage('Setup Helm') {
            steps {
                sh '''
                echo "Setting up Helm..."
                helm version
                '''
            }
        }

        stage('Deploy Solace PubSubPlus Helm Chart') {
            steps {
                sh '''
                export KUBECONFIG=$KUBE_CONFIG
                cd chart
                helm upgrade --install $HELM_RELEASE_NAME . --namespace $HELM_NAMESPACE --create-namespace
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl get pods -n $HELM_NAMESPACE
                '''
            }
        }
    }
}
