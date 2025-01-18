pipeline {
    agent any

    environment {
        PROJECT_ID = 'my-first-devops-project-444911'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')  // Service account credential
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/saleemafroze/appengine-poc.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Create and activate a Python virtual environment
                    sh '''
                    # Check if requirements.txt exists
                    if [ -f "requirements.txt" ]; then
                        # Create a virtual environment
                        python3 -m venv /tmp/venv

                        # Activate the virtual environment and install dependencies
                        source /tmp/venv/bin/activate

                        # Upgrade pip and install dependencies
                        pip install --upgrade pip
                        pip install -r requirements.txt
                    else
                        echo "requirements.txt not found!"
                        exit 1
                    fi
                    '''
                }
            }
        }

        stage('Deploy to Google App Engine') {
            steps {
                script {
                    // Check if gcloud is installed
                    sh 'gcloud --version'

                    // Authenticate with Google Cloud
                    sh 'gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}'

                    // Set project ID
                    sh 'gcloud config set project $PROJECT_ID'

                    // Deploy to App Engine
                    sh 'gcloud app deploy --quiet'  // Use --quiet to suppress prompts
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            cleanWs()  // Optional: clean workspace after the pipeline
        }

        success {
            echo 'Deployment successful!'
        }

        failure {
            echo 'Deployment failed!'
        }
    }
}
