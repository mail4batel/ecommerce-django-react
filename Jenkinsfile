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

        stage('Build') {
            steps {
                sh '''
                echo "Updating system packages..."
                apt-get update -y

                echo "Installing dependencies using apt..."
                apt-get install -y python3 python3-pip nodejs npm

                echo "Setting up virtual environment and installing dependencies..."
                python3 -m venv .venv
                . .venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt

                echo "Building frontend..."
                cd frontend
                npm install
                npm run build
                cd ..

                echo "Running database migrations..."
                .venv/bin/python manage.py makemigrations
                .venv/bin/python manage.py migrate

                echo "Collecting static files..."
                .venv/bin/python manage.py collectstatic --noinput

                echo "Starting Django development server..."
                nohup .venv/bin/python manage.py runserver 0.0.0.0:8000 &
                '''
            }
        }

        stage('Testing') {
            steps {
                script {
                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                        sh '''
                        echo "Activating virtual environment..."
                        . .venv/bin/activate

                        echo "Running tests..."
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