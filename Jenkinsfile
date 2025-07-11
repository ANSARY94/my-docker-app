pipeline {
    agent any  // Runs on any available Jenkins agent
    
    environment {
        NEXUS_REGISTRY = 'localhost:8082'   // Nexus Docker registry port
        NEXUS_REPO = 'docker-hosted'        // Your Nexus repo name
        COMPOSE_FILE = 'docker-compose.yml'  // Your compose file
    }
    
    stages {
        // Stage 1: Pull images from Nexus
        stage('Pull Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-creds',  // Your Jenkins credentials ID
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh '''
                        docker login -u $NEXUS_USER -p $NEXUS_PASS $NEXUS_REGISTRY
                        docker pull $NEXUS_REGISTRY/$NEXUS_REPO/webapp:latest
                        docker pull $NEXUS_REGISTRY/$NEXUS_REPO/pythonapp:latest
                    '''
                }
            }
        }
        
        // Stage 2: Deploy with Docker Compose
        stage('Deploy') {
            steps {
                sh "docker-compose -f $COMPOSE_FILE up -d"
            }
        }
    }
    
    // Clean up after pipeline runs
    post {
        always {
            sh "docker logout $NEXUS_REGISTRY"
        }
    }
}
