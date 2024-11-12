pipeline {
    agent any

    environment {
        SONARQUBE_SCANNER_HOME = tool(name: 'SonarQube Scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation')
        SONAR_PROJECT_KEY = 'your-sonar-project-key'
        SONAR_PROJECT_NAME = 'Your Project Name'
        SONAR_ORGANIZATION = 'your-organization'
    }

    stages {
        stage('Install Dependencies') {
            steps {
                script {
                    sh 'npm ci'
                }
            }
        }

        stage('Lint Code') {
            steps {
                script {
                    // Call the lint code function
                    runLintCode()
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Call the build function
                    runBuild()
                }
            }
        }

        stage('SonarQube Scan') {
            when {
                branch 'main'
            }
            steps {
                script {
                    // Call the SonarQube scan function
                    runSonarQubeScan()
                }
            }
        }

        stage('Home Page Test') {
            steps {
                script {
                    // Call the Home Page Test function
                    runHomePageTest()
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished. Starting cleanup...'
            cleanUpDockerImages()
        }
    }
}

// Define a reusable function for linting code
def runLintCode() {
    try {
        echo "Running ESLint..."
        sh 'npm run lint'
        echo "Linting completed successfully."
    } catch (Exception e) {
        echo "An error occurred during linting: ${e}"
    }
}

// Define a reusable function for building the project
def runBuild() {
    try {
        echo "Building the project..."
        sh 'npm run build'
        echo "Build completed successfully."
    } catch (Exception e) {
        echo "An error occurred during the build: ${e}"
    }
}

// Define a reusable function for SonarQube scan
def runSonarQubeScan() {
    try {
        echo "Starting SonarQube scan..."
        sh "${SONARQUBE_SCANNER_HOME}/bin/sonar-scanner " +
           "-Dsonar.projectKey=${SONAR_PROJECT_KEY} " +
           "-Dsonar.projectName=${SONAR_PROJECT_NAME} " +
           "-Dsonar.organization=${SONAR_ORGANIZATION} " +
           "-Dsonar.sources=." +
           "-Dsonar.typescript.tsconfigPath=tsconfig.json"
        echo "SonarQube scan completed successfully."
    } catch (Exception e) {
        echo "An error occurred during the SonarQube scan: ${e}"
    }
}

// Define a reusable function for the Home Page Test
def runHomePageTest() {
    try {
        echo "Starting Docker Compose services..."
        sh 'docker-compose up -d'

        echo "Launching Vite preview..."
        sh 'npm run preview'

        echo "Home page test completed successfully."
    } catch (Exception e) {
        echo "An error occurred during the Home Page Test: ${e}"
    } finally {
        echo "Stopping Docker Compose services..."
        sh 'docker-compose down'
    }
}

// Define a cleanup function for Docker images
def cleanUpDockerImages() {
    try {
        echo "Cleaning up Docker images..."
        sh 'docker image prune -af'
        echo "Docker image cleanup completed."
    } catch (Exception e) {
        echo "An error occurred during Docker image cleanup: ${e}"
    }
}
