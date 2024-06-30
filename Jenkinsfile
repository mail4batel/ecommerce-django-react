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

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh '''
                    echo "Building Docker image for frontend..."
                    docker build -t ecommerce-frontend .
                    '''
                }
            }
        }

        stage('Build Backend and Run Migrations') {
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

        stage('Run Backend Server') {
            steps {
                sh '''
                echo "Starting Django development server..."
                . ${VENV_DIR}/bin/activate
                nohup ${VENV_DIR}/bin/python manage.py runserver 0.0.0.0:8000 &
                '''
            }
        }

        stage('Test Frontend') {
            steps {
                dir('frontend') {
                    sh '''
                    echo "Running frontend tests..."
                    npm test
                    '''
                }
            }
        }

        stage('Testing Backend') {
            steps {
                script {
                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                        sh '''
                        echo "Activating virtual environment..."
                        . ${VENV_DIR}/bin/activate

                        echo "Running backend tests..."
                        pytest --html=./report.html
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
    }
}

	
