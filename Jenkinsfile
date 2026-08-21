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
                    echo "===== Project Files ====="
                    ls -la
                    echo "===== Backend ====="
                    ls -la backend
                    echo "===== Frontend ====="
                    ls -la frontend
                    echo "===== Deploy ====="
                    ls -la deploy
                '''
            }
        }
    }
}
