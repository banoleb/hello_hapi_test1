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
                ACCESS_KEY = credentials('test1') 
            }
            steps {
                sh 'printenv | grep ACCESS_KEY'
                sh 'echo "Service user is $ACCESS_KEY_USR"'
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
