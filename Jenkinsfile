pipeline {
    agent any

    environment {
        SONAR_TOKEN = 'squ_842fdae8b2f69eb6f608b9387f6bd5542eac7a29'
        SONAR_HOST_URL = 'http://23.22.108.1:9000'
        DOCKER_USER = 'pradhisha'
        DOCKER_PASS = credentials('dockerhub-credentials-id') 
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Pradhisha-N/java-microservices.git', branch: 'main'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "mvn sonar:sonar -Dsonar.projectKey=java-microservices -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.login=$SONAR_TOKEN"
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials-id', 
                    usernameVariable: 'DOCKER_USER', 
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker build -t pradhisha/my-app:latest .
                        docker push pradhisha/my-app:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
               
                    sh 'kubectl apply -f k8s/deployment.yaml --validate=false'
                }
            }
        
    }
}
