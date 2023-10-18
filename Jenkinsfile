#!/usr/bin/env groovy

pipeline {

    agent {
        node {
            label 'ubuntu'
        }
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'npm test'
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'app.js', followSymlinks: false    
        }
    }

}
