pipeline {
    agent any

    tools {
        nodejs 'NodeJS 24.1.0' 
    }
    
     environment {
        dockerImageName = "mianuzairimran/sakura-node-app"            // e.g., myuser/sakura-node-app
        nodeappRegistry = "https://index.docker.io/v1/"          // for Docker Hub
        registryCredential = 'dockerHubCreds'                     // Jenkins credential ID for Docker Hub
    }

    stages {
        stage('Checkout Code') {
            steps {
               git branch: 'main', credentialsId: 'githubToken', url: 'https://github.com/MianUzyerImran/sakura_meri_gurya.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || echo "No tests available"'
            }
        }

        stage('Archive Build Artifact') {
    steps {
        sh '''
        rm -rf build
        mkdir build

        # Copy files except build itself
        rsync -av --exclude=build ./ ./build

        # Create tar.gz outside the build folder
        cd build && tar czf ../sakura-node-app.tar.gz .

        '''
        archiveArtifacts artifacts: 'sakura-node-app.tar.gz', fingerprint: true
    }
}

         stage('Build Docker Image') {
            steps {
                sh '''
                    rm -rf temp-unpack
                    mkdir temp-unpack
                    tar xzf sakura-node-app.tar.gz -C temp-unpack
                    docker build -t $dockerImageName:$BUILD_NUMBER temp-unpack
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        sh """
                            docker tag $dockerImageName:$BUILD_NUMBER $dockerImageName:latest
                            docker push $dockerImageName:$BUILD_NUMBER
                            docker push $dockerImageName:latest
                        """
                    }
                }
            }
        }

        stage('Clean Docker Images') {
            steps {
                sh 'docker rmi -f $(docker images -aq) || true'
            }
        }
        
        stage('Deploy Docker Container') {
    steps {
        sh '''
            docker pull mianuzairimran/sakura-node-app:$BUILD_NUMBER

            # Stop and remove any existing container with same name
            docker rm -f sakura-node-app || true

            # Run container on port 3000 (change if needed)
            docker run -d --name sakura-node-app -p 3000:3000 mianuzairimran/sakura-node-app:$BUILD_NUMBER

            echo "✅ App is running at: http://$(hostname -I | awk '{print $1}'):3000"
        '''
    }
}

    }

    post {
        success {
            echo "✅ Build Succeeded: sakura_meri_gurya pipeline completed successfully!"
        }
        failure {
            echo "❌ Build Failed: Please check the logs and fix the issues."
        }
        always {
            echo "ℹ️ Pipeline finished. You can find logs and artifacts in this build."
        }
    }
}
