pipeline {
    agent any

    environment {
        HELM_CHART_REPO = "https://github.com/TAM360/solace-jenkins"
        HELM_RELEASE_NAME = "pubsubplus"
        HELM_NAMESPACE = "default"
        // KUBECONFIG = credentials('kubeconfig-id') // Store kubeconfig in Jenkins credentials as text
        KUBECONFIG = credentials("solace-k8s-config")
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

        // stage('Deploy Solace PubSubPlus Helm Chart') {
        //     steps {
        //         sh '''
        //         echo $
        //         cd chart
        //         helm upgrade --install $HELM_RELEASE_NAME . --namespace $HELM_NAMESPACE --create-namespace
        //         '''
        //     }
        // }

        stage('Deploy Solace EventBroker CRD.') {
            steps {
                sh '''
                cd operator
                kubectl apply -f ha-mode.yaml
                sleep 30
                kubectl get pods -n $HELM_NAMESPACE
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
