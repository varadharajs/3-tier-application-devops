pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/varadharajs/3-tier-application-devops.git'
            }
        }

        stage('Verify Code') {
            steps {
                sh '''
                    echo "======================================"
                    echo "        PROJECT FILES"
                    echo "======================================"

                    ls -la

                    echo ""
                    echo "===== Backend ====="
                    ls -la backend

                    echo ""
                    echo "===== Frontend ====="
                    ls -la frontend

                    echo ""
                    echo "===== Deploy ====="
                    ls -la deploy
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    echo "======================================"
                    echo "   DOCKER BUILD ON DEPLOYMENT SERVER"
                    echo "======================================"

                    ssh -o StrictHostKeyChecking=no \
                        -i /var/lib/jenkins/.ssh/id_ed25519 \
                        ec2-user@172.31.14.222 '

                        echo "===== Connected to Deployment Server ====="
                        hostname

                        echo ""
                        echo "===== Docker Version ====="
                        docker --version

                        echo ""
                        echo "===== Docker Compose Version ====="
                        docker compose version

                        echo ""
                        echo "===== Application Directory ====="
                        cd /home/ec2-user/Jerney

                        pwd

                        echo ""
                        echo "===== Building Docker Images ====="
                        docker compose build

                        echo ""
                        echo "===== Docker Images ====="
                        docker images

                        echo ""
                        echo "===== Docker Build Completed ====="
                    '
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                    echo "======================================"
                    echo "       DEPLOYING APPLICATION"
                    echo "======================================"

                    ssh -o StrictHostKeyChecking=no \
                        -i /var/lib/jenkins/.ssh/id_ed25519 \
                        ec2-user@172.31.14.222 '

                        cd /home/ec2-user/Jerney

                        echo "===== Starting Application ====="
                        docker compose up -d

                        echo ""
                        echo "===== Waiting for Containers ====="
                        sleep 10

                        echo ""
                        echo "===== Application Status ====="
                        docker compose ps
                    '
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    echo "======================================"
                    echo "          HEALTH CHECK"
                    echo "======================================"

                    ssh -o StrictHostKeyChecking=no \
                        -i /var/lib/jenkins/.ssh/id_ed25519 \
                        ec2-user@172.31.14.222 '

                        cd /home/ec2-user/Jerney

                        echo "===== Container Status ====="
                        docker compose ps

                        echo ""
                        echo "===== Frontend Health Check ====="

                        curl -f http://127.0.0.1:3000 > /dev/null

                        if [ $? -eq 0 ]; then
                            echo "Frontend is UP"
                        else
                            echo "Frontend health check FAILED"
                            exit 1
                        fi

                        echo ""
                        echo "===== PostgreSQL Status ====="
                        docker compose ps postgres

                        echo ""
                        echo "===== Application Health Check PASSED ====="
                    '
                '''
            }
        }
    }

    post {

        success {
            echo '''
======================================
       JENKINS PIPELINE SUCCESS
======================================
Checkout       : SUCCESS
Code Verify    : SUCCESS
Docker Build   : SUCCESS
Deployment     : SUCCESS
Health Check   : SUCCESS
======================================
'''
        }

        failure {
            echo '''
======================================
       JENKINS PIPELINE FAILED
======================================
Check the Console Output for
the failed stage.
======================================
'''
        }

        always {
            echo 'Jenkins pipeline execution completed.'
        }
    }
}
