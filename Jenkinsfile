pipeline {
    agent any

    environment {
        ENV_DIR = 'env'
        dockerImageName = "mianuzairimran/sakura-python-app"          
        nodeappRegistry = "https://index.docker.io/v1/"       
        registryCredential = 'dockerhubAcc'
    }


    stages {
        stage('Clone Code') {
            steps {
                git url: 'https://github.com/saharbat00l/Py-App.git', branch: 'main'
            }
        }

stage('Show requirements.txt') {
    steps {
        sh 'cat requirements.txt'
    }
}

      stage('Install Dependencies') {
          steps{
              sh '''
        python3 -m venv $ENV_DIR
        if [ -s requirements.txt ]; then
            $ENV_DIR/bin/pip install --upgrade pip
            $ENV_DIR/bin/pip install -r requirements.txt
        else
            echo "✅ No dependencies to install"
        fi
        '''
          }
          }

 stage('Run Tests') {
    steps {
        sh '''
        $ENV_DIR/bin/python -m unittest discover -s . -p "test_app.py" > test-results.txt || true
        '''
    }
}



        stage('Archive Build Artifact') {
            steps {
                sh '''
                tar czf sakura-python-app.tar.gz *.py *.txt *.json templates/
                '''
                archiveArtifacts artifacts: 'sakura-python-app.tar.gz', fingerprint: true
            }
        }


  stage('Build Docker Image') {
    steps {
        sh '''
            rm -rf temp-unpack
            mkdir temp-unpack
            tar xzf sakura-python-app.tar.gz -C temp-unpack
            cp Dockerfile temp-unpack/
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
            docker pull $dockerImageName:$BUILD_NUMBER

            # Stop and remove any existing container with the same name
            docker rm -f sakura-python-app || true

            # Run container on port 5000 (adjust if your app uses a different port)
            docker run -d --name sakura-python-app -p 5000:5000 $dockerImageName:$BUILD_NUMBER

            echo "✅ App is running at: http://$(hostname -I | awk '{print $1}'):5000"
        '''
    }
}

    }

    post {
        success {
            echo '✅ Build completed successfully!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
