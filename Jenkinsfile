pipeline {
    agent none
    stages {
        stage('Integration UI Test') {
            parallel {
                stage('Deploy') {
                    agent any
                    steps {
                        sh 'echo "Starting deploy script"'
                        sh './jenkins/scripts/deploy.sh'
                        sh 'echo "Deploy script finished"'
                        input message: 'Finished using the web site? (Click "Proceed" to continue)'
                        sh 'echo "Starting kill script"'
                        sh './jenkins/scripts/kill.sh'
                        sh 'echo "Kill script finished"'
                    }
                }
                stage('Headless Browser Test') {
                    agent {
                        docker {
                            image 'maven:3-alpine'
							args '-v /root/.m2:/root/.m2'
                        }
                    }
                    steps {
                        // Ensure source code is checked out
                        checkout scm
                        // Run Maven clean package
                        sh 'echo "Starting Maven clean package"'
                        sh 'mvn -B -DskipTests clean package'
                        sh 'echo "Finished Maven clean package"'
                        // Run Maven tests
                        sh 'echo "Starting Maven tests"'
                        sh 'mvn test'
                        sh 'echo "Finished Maven tests"'
                    }
                    post {
                        always {
                            junit 'target/surefire-reports/*.xml'
                        }
                        failure {
                            echo 'Headless Browser Test stage failed'
                        }
                    }
                }
            }
        }
    }
}
