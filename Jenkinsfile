pipeline {
    agent any

    tools {
        // Must match the Maven name configured in Manage Jenkins -> Tools
        maven 'maven-3.8.5'
    }

    stages {
        stage('Build') {
            steps {
                // Compiles the app and runs the unit tests
                sh 'mvn -B clean package'
            }
        }

        stage('Publish Test Results') {
            steps {
                junit allowEmptyResults: true, testResults: '**/target/surefire-reports/TEST-*.xml'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Builds the image from the Dockerfile, tagged with the Jenkins build number.
                // Image stays on the Jenkins machine - nothing is pushed anywhere.
                sh "docker build -t hello-world-java:${env.BUILD_NUMBER} ."
            }
        }

        stage('Archive Jar') {
            steps {
                // The built jar is saved with the build, downloadable from the Jenkins UI
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Build succeeded. The jar is available in the build artifacts.'
        }
        failure {
            echo 'Build failed. Check the console output and test results.'
        }
    }
}
