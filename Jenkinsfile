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

        stage('Setup Virtual Environment') {
            steps {
                script {
                    // Navigate to workspace
                    dir('/var/lib/jenkins/workspace/jenkins-docker-test') {
                        // Create and activate virtual environment
                        sh '''
                        pip install bzt
                        '''
                    }
                }
            }
        }

        // stage('Setup Taurus') {
        //     steps {
        //         script {
        //             sh "sudo pip install bzt"
        //             // sh "pip install virtualenv"
        //         }
        //     }
        // }

        stage('Taurus test') {
            steps {
                script {
                    sh "wget https://raw.githubusercontent.com/Blazemeter/taurus/master/examples/jmeter/stepping.yml"
                }
            }
        }

        stage('Get config file') {
            steps {
                script {
                    sh "wget https://raw.githubusercontent.com/Blazemeter/taurus/master/examples/jmeter/stepping.yml"
                }
            }
        }

        stage('Run test') {
            steps {
                script {
                    sh "bzt stepping.yml"
                }
            }
        }

        stage('JMeter Test') {
            steps {
                script {
                    // Run the JMeter test
                    echo "Running JMeter tests..."
                    def timestamp = sh(script: 'date +%Y%m%d%H%M%S', returnStdout: true).trim()
                    sh '''
                    # Download JMeter if not already available
                    if [ ! -f apache-jmeter-5.6.3.zip ]; then
                        wget https://downloads.apache.org/jmeter/binaries/apache-jmeter-5.6.3.zip
                        unzip apache-jmeter-5.6.3.zip
                    fi

                    rm -rf results/*

                    # Run JMeter test plan     
                    apache-jmeter-5.6.3/bin/jmeter -n -t test_plan.jmx -l results_${timestamp}.jtl -e -o results_${timestamp}
                    '''
                    //apache-jmeter-5.6.3/bin/jmeter -n -t test_plan.jmx -l results.jtl -e -o results
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
        success {
            echo 'Pipeline succeeded!'
            sh 'docker rm -f flask-hello-world'
            // cleanWs()
        }
        failure {
            echo 'Pipeline failed!'
            sh 'docker rm -f flask-hello-world'
            // cleanWs()
        }
        // always {
        //     // Archive the test results
        //     archiveArtifacts artifacts: 'results/**', allowEmptyArchive: true
        //     // Publish the HTML report
        //     publishHTML(target: [
        //         allowMissing: true,
        //         alwaysLinkToLastBuild: true,
        //         keepAll: true,
        //         reportDir: 'results',
        //         reportFiles: 'index.html',
        //         reportName: 'JMeter Test Report'
        //     ])
        // }
    }
}
