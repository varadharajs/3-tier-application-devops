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
                        echo "===== Going to Application Directory ====="
                        cd /home/ec2-user/Jerney

                        echo ""
                        echo "===== Current Directory ====="
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
    }

    post {

        success {
            echo '======================================'
            echo '       JENKINS BUILD SUCCESSFUL'
            echo '======================================'
        }

        failure {
            echo '======================================'
            echo '       JENKINS BUILD FAILED'
            echo '======================================'
        }

        always {
            echo 'Jenkins pipeline execution completed.'
        }
    }
}
