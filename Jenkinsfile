pipeline {
    agent any

    stages {

        stage('Clone Repo') {
            steps {
                script {
                    git url: 'https://github.com/bengo1990/bens-cool-api', branch: 'main'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    echo "Building Docker image..."
                    // sh 'which docker'
                    sh 'docker build -t flask-hello-world .'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Run the Docker container
                    echo "Running Docker container..."
                    sh 'docker run -d -p 5000:5000 --name flask-hello-world flask-hello-world'
                }
            }
        }

        stage('JMeter Test') {
            steps {
                script {
                    // Run the JMeter test
                    echo "Running JMeter tests..."
                    sh '''
                    # Download JMeter if not already available
                    if [ ! -f apache-jmeter-5.5.zip ]; then
                        wget https://downloads.apache.org//jmeter/binaries/apache-jmeter-5.5.zip
                        unzip apache-jmeter-5.5.zip
                    fi

                    # Run JMeter test plan (assuming you have a JMeter test plan saved as 'test_plan.jmx')
                    apache-jmeter-5.5/bin/jmeter -n -t test_plan.jmx -l results.jtl -e -o results
                    '''
                }
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    // Stop and remove the Docker container
                    echo "Cleaning up Docker container..."
                    sh 'docker stop flask-hello-world || true && docker rm flask-hello-world || true'
                }
            }
        }
    }

    post {
        always {
            // Archive the test results
            archiveArtifacts artifacts: 'results/**', allowEmptyArchive: true
            // Publish the HTML report
            publishHTML(target: [
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'results',
                reportFiles: 'index.html',
                reportName: 'JMeter Test Report'
            ])
        }
    }
}
