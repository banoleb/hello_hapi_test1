#!/usr/bin/env groovy

pipeline {

    agent {
        node {
            label 'ubuntu'
        }
    }

    stages {
        stage('Example') {
            environment { 
                ACCESS_KEY = credentials('github-token') 
            }
            steps {
                sh 'printenv | grep ACCESS_KEY'
            }
        }
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
