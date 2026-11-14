pipeline {
    agent any
    tools {
        maven 'mymaven'
    }
    stages {
        stage('Source-code') {
            steps {
                git branch: 'main', url: 'https://github.com/Surekha-hash/docker-webapp-k8s.git'
            }
        }
        stage('maven build') {
            steps {
                sh "mvn clean install"
            }
        }
        stage('sonar scanning') {
            steps {
                withSonarQubeEnv('mysonar') {
                    sh "mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=myproject"
            }
        }
    }
    stage('quality gate') {
        steps {
            waitForQualityGate abortPipeline: true, credentialsId: 'sonar-cred'
        }
    }
    stage('artifacts') {
        steps {
            echo "Add you nexus script here"
        }
    }
    stage('docker build ') {
        steps {
            sh 'docker build -t appimage .'
            sh 'docker build -t dbimage Docker-db'
        }
    }
    stage('trivy scan') {
        steps {
            sh 'trivy image appimage >> appimage-report.txt'
            sh 'trivy image dbimage >> dbimage-report.txt'
        }
    }
    stage('push to dockerhub') {
        steps {
            script {
                withDockerRegistry(credentialsId: 'dockerhub') {
                    sh 'docker tag appimage surekha91227/webapp:app'
                    sh 'docker tag dbimage surekha91227/webapp:db'
                    sh 'docker push surekha91227/webapp:app'
                    sh 'docker push surekha91227/webapp:db'
                }
            }
        }
    }
    stage('docker compose') {
        steps {
            //sh 'docker-compose build'
            sh 'docker-compose up -d'
            sleep 10
            sh 'docker ps'
        }
    }
}
}
