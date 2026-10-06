pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Create Environment') {
            steps {
                sh '''
                    if [ ! -f .env ]; then
                        cp .env.example .env
                    fi
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker compose build
                '''
            }
        }

        stage('Start Containers') {
            steps {
                sh '''
                    docker compose down || true
                    docker compose up -d
                '''
            }
        }

stage('Laravel Setup') {
    steps {
        sh '''
            docker compose exec -T app sh -c 'cp .env.example .env'

            docker compose exec -T app php artisan key:generate --force

            docker compose exec -T app php artisan migrate --force

            docker compose exec -T app php artisan optimize:clear

            docker compose exec -T app php artisan config:cache

            docker compose exec -T app php artisan route:cache

            docker compose exec -T app php artisan view:cache
        '''
    }
}

        stage('Verify') {
            steps {
                sh '''
                    docker compose ps

                    sleep 5

                    curl -f http://localhost:4000
                '''
            }
        }
    }

    post {
        success {
            echo 'Laravel Hotel deployment successful'
        }

        failure {
            echo 'Laravel Hotel deployment failed'
        }
    }
}