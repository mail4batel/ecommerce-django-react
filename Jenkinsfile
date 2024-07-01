pipeline {
    agent any

    environment {
        VENV_DIR = '.venv'
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Clone the repository
                git branch: 'main', url: 'https://github.com/mail4batel/ecommerce-django-react.git'
            }
        }

        stage('Setup Virtual Environment') {
            steps {
                sh '''
                echo "Setting up virtual environment..."
                python3 -m venv ${VENV_DIR}
                . ${VENV_DIR}/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Build and Run Frontend Docker Container') {
            steps {
                dir('frontend') {
                    sh '''
                    echo "Building Docker image for frontend..."
                    docker build -t frontend-image .

                    echo "Stopping and removing any existing frontend Docker container..."
                    docker stop frontend-container || true
                    docker rm frontend-container || true

                    echo "Running frontend Docker container..."
                    docker run -d -p 8081:80 --name frontend-container frontend-image
                    '''
                }
            }
        }

        stage('Build Backend') {
            steps {
                sh '''
                echo "Activating virtual environment..."
                . ${VENV_DIR}/bin/activate

                echo "Running database migrations..."
                python manage.py makemigrations
                python manage.py migrate

                echo "Collecting static files..."
                python manage.py collectstatic --noinput
                '''
            }
        }

        stage('Run Server') {
            steps {
                sh '''
                echo "Starting Django development server..."
                . ${VENV_DIR}/bin/activate
                nohup ${VENV_DIR}/bin/python manage.py runserver 0.0.0.0:8000 &
                '''
            }
        }

        stage('Testing') {
            steps {
                script {
                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                        sh '''
                        echo "Activating virtual environment..."
                        . ${VENV_DIR}/bin/activate

                        echo "Running backend tests..."
                        pytest --html-report=./report.html
                        '''
                    }
                }
            }
        }

        stage('Publishing Report') {
            steps {
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: '.',
                    reportFiles: 'report.html',
                    reportName: 'Test Report',
                    reportTitles: 'Test Report'
                ])
            }
        }

        stage('Cleanup') {
            steps {
                sh '''
                echo "Stopping and removing frontend Docker container..."
                docker stop frontend-container
                docker rm frontend-container
                '''
            }
        }
    }
}