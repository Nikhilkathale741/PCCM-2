pipeline {
    agent any

    environment {
        // Reference your GitHub credentials ID here
        GIT_CREDENTIALS = 'gitiid'
        // If you have a .env file, you can reference it here or manage secrets via Jenkins credentials plugins
    }

    stages {
        stage('Checkout') {
            steps {
                // Fetch code from GitHub using credentials
                git branch: 'main', 
                    credentialsId: "${env.GIT_CREDENTIALS}", 
                    url: 'https://github.com/Nikhilkathale741/PCCM-2.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    sh 'docker-compose build'
                }
            }
        }

        stage('Run Migrations') {
            steps {
                script {
                    sh 'docker-compose run web python PCCM/manage.py migrate'
                }
            }
        }

        stage('Collect Static Files') {
            steps {
                script {
                    sh 'docker-compose run web python PCCM/manage.py collectstatic --noinput'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh 'docker-compose run web python PCCM/manage.py test'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Stop old containers and start new ones
                    sh 'docker-compose down'
                    sh 'docker-compose up -d'
                }
            }
        }
    }

    post {
        always {
            // Clean up dangling containers/images if needed
            sh 'docker system prune -f'
        }
        failure {
            echo 'Build or deployment failed!'
        }
        success {
            echo 'Deployment succeeded!'
        }
    }
}
