pipeline {

    agent any

    environment {
        REPO_URL = 'https://github.com/varadharajs/3-tier-application-devops.git'

        DEPLOY_USER = 'ec2-user'
        DEPLOY_HOST = '172.31.14.222'
        DEPLOY_DIR  = '/home/ec2-user/Jerney'

        SSH_KEY = '/var/lib/jenkins/.ssh/id_ed25519'
    }

    stages {

        // =====================================================
        // 1. CHECKOUT CODE
        // =====================================================
        stage('Checkout') {
            steps {
                checkout scm
            }
        }


        // =====================================================
        // 2. VERIFY CODE
        // =====================================================
        stage('Verify Code') {
            steps {
                sh '''
                    echo "======================================"
                    echo "        VERIFY PROJECT"
                    echo "======================================"

                    echo "===== Project Files ====="
                    ls -la

                    echo "===== Backend ====="
                    ls -la backend

                    echo "===== Frontend ====="
                    ls -la frontend

                    echo "===== Docker Compose ====="
                    ls -la docker-compose.yml

                    echo "===== Jenkinsfile ====="
                    ls -la Jenkinsfile
                '''
            }
        }


        // =====================================================
        // 3. TEST JENKINS
        // =====================================================
        stage('Test Jenkins') {
            steps {
                echo '======================================'
                echo '        Jenkins is working!'
                echo '======================================'
            }
        }


        // =====================================================
        // 4. TEST SSH + DOCKER ON PRODUCTION
        // =====================================================
        stage('Test Production SSH') {
            steps {
                sh '''
                    echo "===== Testing Production Server ====="

                    ssh -o StrictHostKeyChecking=no \
                        -i ${SSH_KEY} \
                        ${DEPLOY_USER}@${DEPLOY_HOST} \
                        'hostname && docker --version && docker compose version'
                '''
            }
        }


        // =====================================================
        // 5. DEPLOY TO PRODUCTION
        // =====================================================
        stage('Deploy to Production') {
            steps {
                sh '''
                    echo "======================================"
                    echo "     DEPLOYING TO PRODUCTION"
                    echo "======================================"

                    ssh -o StrictHostKeyChecking=no \
                        -i ${SSH_KEY} \
                        ${DEPLOY_USER}@${DEPLOY_HOST} << 'EOF'

                    set -e

                    echo "======================================"
                    echo "1. Go to application directory"
                    echo "======================================"

                    cd /home/ec2-user/Jerney

                    pwd


                    echo "======================================"
                    echo "2. Check Git repository"
                    echo "======================================"

                    git status


                    echo "======================================"
                    echo "3. Pull latest code"
                    echo "======================================"

                    git pull --rebase origin main


                    echo "======================================"
                    echo "4. Show latest commit"
                    echo "======================================"

                    git log -1 --oneline


                    echo "======================================"
                    echo "5. Check project files"
                    echo "======================================"

                    ls -la


                    echo "======================================"
                    echo "6. Check Docker"
                    echo "======================================"

                    docker --version
                    docker compose version


                    echo "======================================"
                    echo "7. Build Docker images"
                    echo "======================================"

                    docker compose build


                    echo "======================================"
                    echo "8. Start containers"
                    echo "======================================"

                    docker compose up -d


                    echo "======================================"
                    echo "9. Container status"
                    echo "======================================"

                    docker compose ps


                    echo "======================================"
                    echo "10. Running containers"
                    echo "======================================"

                    docker ps


                    echo "======================================"
                    echo "DEPLOYMENT COMPLETED"
                    echo "======================================"

EOF
                '''
            }
        }


        // =====================================================
        // 6. VERIFY PRODUCTION
        // =====================================================
        stage('Verify Production') {
            steps {
                sh '''
                    echo "======================================"
                    echo "     VERIFY PRODUCTION"
                    echo "======================================"

                    echo "===== Testing Frontend ====="

                    ssh -o StrictHostKeyChecking=no \
                        -i ${SSH_KEY} \
                        ${DEPLOY_USER}@${DEPLOY_HOST} \
                        'curl -I http://127.0.0.1:3000'

                    echo "======================================"
                    echo "Production verification completed"
                    echo "======================================"
                '''
            }
        }
    }


    // =====================================================
    // POST ACTIONS
    // =====================================================
    post {

        success {
            echo '''
======================================
       CI/CD DEPLOYMENT SUCCESS
======================================
Application successfully deployed!
======================================
'''
        }

        failure {
            echo '''
======================================
       CI/CD DEPLOYMENT FAILED
======================================
Check Jenkins Console Output
======================================
'''
        }

        always {
            echo 'Jenkins Pipeline completed.'
        }
    }
}
