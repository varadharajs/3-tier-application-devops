pipeline {

    agent any

    environment {
        GITHUB_REPO = 'https://github.com/varadharajs/3-tier-application-devops.git'

        PROD_USER = 'ec2-user'
        PROD_HOST = '172.31.14.222'
        PROD_DIR  = '/home/ec2-user/3-tier-application-devops'

        SSH_KEY = '/var/lib/jenkins/.ssh/id_ed25519'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: "${GITHUB_REPO}"
            }
        }

        stage('Verify Code') {
            steps {
                sh '''
                    echo "===== Project Files ====="
                    ls -la

                    echo "===== Backend ====="
                    ls -la backend

                    echo "===== Frontend ====="
                    ls -la frontend

                    echo "===== Docker Compose ====="
                    ls -la docker-compose.yml
                '''
            }
        }

        stage('Test Jenkins') {
            steps {
                echo 'Jenkins is working!'
            }
        }

        stage('Test Production SSH') {
            steps {
                sh '''
                    ssh -o StrictHostKeyChecking=no \
                    -i ${SSH_KEY} \
                    ${PROD_USER}@${PROD_HOST} \
                    "hostname && docker --version && docker compose version"
                '''
            }
        }

        stage('Deploy to Production') {
            steps {
                sh '''
                    ssh -o StrictHostKeyChecking=no \
                    -i ${SSH_KEY} \
                    ${PROD_USER}@${PROD_HOST} << 'EOF'

                    set -e

                    cd ${PROD_DIR}

                    echo "===== Pull latest code ====="
                    git pull origin main

                    echo "===== Docker Compose Build ====="
                    docker compose build

                    echo "===== Stop old containers ====="
                    docker compose down

                    echo "===== Start new containers ====="
                    docker compose up -d

                    echo "===== Container Status ====="
                    docker compose ps

                    echo "===== Production Deployment Completed ====="

                    EOF
                '''
            }
        }

        stage('Verify Production') {
            steps {
                sh '''
                    ssh -o StrictHostKeyChecking=no \
                    -i ${SSH_KEY} \
                    ${PROD_USER}@${PROD_HOST} \
                    "curl -f http://127.0.0.1:3000 > /dev/null && echo 'Production application is UP'"
                '''
            }
        }
    }

    post {
        success {
            echo '======================================'
            echo ' CI/CD DEPLOYMENT SUCCESSFUL'
            echo '======================================'
        }

        failure {
            echo '======================================'
            echo ' CI/CD DEPLOYMENT FAILED'
            echo ' Check Jenkins Console Output'
            echo '======================================'
        }
    }
}
