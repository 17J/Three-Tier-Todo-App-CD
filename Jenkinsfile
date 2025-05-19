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
                git branch: 'main',
                    changelog: false,
                    credentialsId: 'github-cred',
                    poll: false,
                    url: 'https://github.com/17J/Three-Tier-Todo-App-CD.git'
            }
        }

        stage('Deploy To K8s') {
            steps {
                dir('/var/lib/jenkins/workspace/three-tier-cd/K8s/') {
                    withKubeConfig(
                        caCertificate: '',
                        clusterName: 'expdevops-cluster',
                        contextName: '',
                        credentialsId: 'kube-token',
                        namespace: 'webapps',
                        restrictKubeConfigAccess: false,
                        serverUrl: 'https://E642F0AFE47848DAE52BA10734079C34.gr7.ap-south-1.eks.amazonaws.com'
                    ) {
                        sh """
                            kubectl apply -f db-ds-service.yml -n webapps
                            kubectl apply -f backend-ds-service.yml -n webapps
                            kubectl apply -f frontend-ds-service.yml -n webapps
                            sleep 30
                        """
                    }
                }
            }
        }

        stage('Verify To K8s') {
            steps {
                dir('/var/lib/jenkins/workspace/three-tier-cd/K8s/') {
                    withKubeConfig(
                        caCertificate: '',
                        clusterName: 'expdevops-cluster',
                        contextName: '',
                        credentialsId: 'kube-token',
                        namespace: 'webapps',
                        restrictKubeConfigAccess: false,
                        serverUrl: 'https://E642F0AFE47848DAE52BA10734079C34.gr7.ap-south-1.eks.amazonaws.com'
                    ) {
                        sh """
                            kubectl get pods -n webapps
                            kubectl get svc -n webapps
                        """
                    }
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment to Kubernetes successful!'
            // Add notification logic here (e.g., email, Slack)
        }
        failure {
            echo 'Deployment to Kubernetes failed!'
            // Add failure notification logic here
        }
    }
}
