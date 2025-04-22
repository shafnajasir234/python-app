pipeline {
    agent any

    environment {
        VENV_DIR = 'venv'
        FLASK_APP = 'app.py'
        FLASK_PORT = '8000'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/shafnajasir234/python-app.git'
            }
        }

        stage('Setup Python Environment') {
            steps {
                script {
                    // Create virtual environment and install dependencies
                    sh '''
                        python3 -m venv $VENV_DIR
                        . $VENV_DIR/bin/activate
                        pip install --upgrade pip
                        pip install flask
                    '''
                }
            }
        }

        stage('Run Flask App') {
            steps {
                script {
                    // Run Flask app in the background
                    sh '''
                        . $VENV_DIR/bin/activate
                        nohup python3 $FLASK_APP > output.log 2>&1 &
                        echo $! > flask_app.pid
                    '''
                }
            }
        }

        stage('Test Flask App') {
            steps {
                script {
                    // Wait for the app to start and then test it
                    sh '''
                        sleep 5  # Wait for Flask to start
                        curl http://localhost:$FLASK_PORT
                    '''
                }
            }
        }
    }

    post {
        always {
            // Clean up by stopping the Flask app
            script {
                sh '''
                    if [ -f flask_app.pid ]; then
                        kill $(cat flask_app.pid) || true
                        rm flask_app.pid
                    fi
                '''
            }
            echo 'Pipeline execution completed!'
        }
    }
}
