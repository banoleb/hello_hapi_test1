#!/usr/bin/env groovy

pipeline {
    agent {
        node {
            label 'ubuntu'
        }
    }
    environment { 
        DIR_APP = '/home/jenkins/app/hello_hapi_test1/'
    }
    parameters {
        choice(name: 'branch', choices: ['staging', 'master'], description: 'git branch')
        string(name: 'server_ip', defaultValue: '167.71.11.147', description: 'Server IP to do hhtp check')
    }
    stages {
        stage('Checkout') {
          steps {
            script {
               git branch: '$branch', url: 'https://github.com/banoleb/hello_hapi_test1.git'
              }
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
        stage('DockerBuild') {
            steps{
                script {
                    sh 'docker rmi -f johnnybravo6/testnpm:${branch}'
                    dockerImage = docker.build "johnnybravo6/testnpm:${branch}"
                }
            }
        }
        stage('DockerPush') {
            steps{
                script {
                    docker.withRegistry("https://registry.hub.docker.com", "docker-private-credentials") {
                        dockerImage.push()
                    }
                }
            }
        }
        stage("DockerDeploy"){
            steps{
                sshPublisher(
                    continueOnError: true, failOnError: false,
                    publishers: [
                        sshPublisherDesc(
                            configName: "ubuntu-vps",
                            verbose: true,
                            transfers: [
                                sshTransfer(execCommand: " git -C $DIR_APP checkout ${branch} "),
                                sshTransfer(execCommand: " git -C $DIR_APP fetch"),
                                sshTransfer(execCommand: " git -C $DIR_APP  pull origin ${branch} "),
                                sshTransfer(execCommand: "docker-compose -f $DIR_APP/docker-compose.yml -p ${branch} down"),
                                sshTransfer(execCommand: "docker-compose -f $DIR_APP/docker-compose.yml -p ${branch} rm -f"),
                                sshTransfer(execCommand: "docker-compose -f $DIR_APP/docker-compose.yml -p ${branch} pull "),
                                sshTransfer(execCommand: "docker-compose -f $DIR_APP/docker-compose.yml -p ${branch} up -d")
                            ]
                        )
                    ]
            )
            }
        }
        stage ('wait'){
            steps{
                sh 'sleep 5'
            }
        }
        stage ('httpcheck'){
            steps {
                script {
                    if (params.branch == 'staging') {
                        httpRequest ignoreSslErrors: true, responseHandle: 'NONE', url: "http://${server_ip}:3000/hello/user", wrapAsMultipart: false
                    }  else {
                        httpRequest ignoreSslErrors: true, responseHandle: 'NONE', url: "http://${server_ip}:4000/hello/user", wrapAsMultipart: false
                        }
                }
            }
        }
    }
}
