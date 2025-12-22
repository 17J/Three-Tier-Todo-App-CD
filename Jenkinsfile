pipeline {
    agent any
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Github Checkout') {
            steps {
                echo 'Checking out GitOps Repository (CD)'
                git branch: 'main',
                    changelog: false,
                    credentialsId: 'github-cred',
                    poll: false,
                    url: 'https://github.com/17J/Three-Tier-Todo-App-CD.git'
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                dir('K8s') {
                    withKubeConfig(credentialsId: 'kube-token',  // Assume ye proper kubeconfig ya token credential hai
                                   serverUrl: 'https://E642F0AFE47848DAE52BA10734079C34.gr7.ap-south-1.eks.amazonaws.com') {
                        echo 'Deploying manifests in order: DB -> Backend -> Frontend'
                        sh '''
                            kubectl apply -f db-ds-service.yml -n webapps
                            kubectl rollout status deployment/db-deployment -n webapps --timeout=300s || exit 1
                            
                            kubectl apply -f backend-ds-service.yml -n webapps
                            kubectl rollout status deployment/backend-deployment -n webapps --timeout=300s || exit 1
                            
                            kubectl apply -f frontend-ds-service.yml -n webapps
                            kubectl rollout status deployment/frontend-deployment -n webapps --timeout=300s || exit 1
                        '''
                    }
                }
            }
        }
        stage('Verify Deployment') {
            steps {
                dir('K8s') {
                    withKubeConfig(credentialsId: 'kube-token',
                                   serverUrl: 'https://E642F0AFE47848DAE52BA10734079C34.gr7.ap-south-1.eks.amazonaws.com') {
                        echo 'Verifying Pods, Services and Ingress'
                        sh '''
                            kubectl get pods -n webapps -o wide
                            kubectl get svc -n webapps
                            kubectl get ingress -n webapps  // Agar ingress hai to
                            kubectl describe pods -n webapps | grep -i "error\|failed"
                        '''
                    }
                }
            }
        }
    }
    post {
        success {
            echo '🚀 Deployment to Kubernetes successful! Application is live.'
            // Add Slack/Email notification here if needed
            // slackSend channel: '#deployments', message: "Success: Three-Tier App deployed to webapps namespace"
        }
        failure {
            echo '❌ Deployment failed! Check logs for details.'
            // slackSend channel: '#deployments', message: "Failed: Three-Tier App deployment failed"
        }
        always {
            echo 'Pipeline completed.'
            // Archive logs ya reports if any
        }
    }
}
