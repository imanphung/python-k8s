#!/usr/bin/env

pipeline {

    environment {
        dockerimagename = "antphungit/flask-demo"
        dockerImage = ""
    }

    agent {
        docker { image 'python:3' }
    }

    stages {
        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }
    
        stage('Build & Test') {
            steps {
                sh 'pip --version'
                sh 'pip install -r requirements.txt'
                sh 'python test_app.py'
                script {
                    dockerImage = docker.build dockerimagename
                }
            }
        }
    
        stage('Pushing Image') {
            environment {
                registryCredential = 'dockerhublogin'
            }
            steps{
                script {
                    docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Deploying Services to Kubernetes Cluster') {
            steps {
                script {
                    kubernetesDeploy(configs: "flask-deployment.yml", kubeconfigId: "kubernetes-samhello")
                }
            }
        }
    }
}
