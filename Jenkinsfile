@Library('my-shared-library') _

pipeline {
    agent any 

    parameters {
        choice(name: 'action', choices: ['create', 'delete'], description: 'Choose create/destroy')
        string(name: 'ImageName', description: 'Name of docker build', defaultValue: 'javaapp')
        string(name: 'ImageTag', description: 'Tag of docker build', defaultValue: 'v1')
        string(name: 'DockerHubUser', description: 'DockerHub user name', defaultValue: 'mounika257')
    }

    stages {
        stage ('checkout') {
            when { expression { params.action == 'create' } }
            steps {
                gitCheckout(
                    branch: 'main',
                    url: 'https://github.com/mounika257/simple-java-maven-app.git'
                )
            }
        }
        stage ('maven test') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnTest()
                }
            }
        }
        stage ('Integration Test maven') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnIntegrationTest()
                }
            }
        }
        stage ('Static code analysis: Sonarqube') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    def SonarQubecredentialsId = 'sonarqube-api'
                    statiCodeAnalysis(SonarQubecredentialsId)
                }
            }
        }
        stage ('Quality Gate Status Check : Sonarqube') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    def SonarQubecredentialsId = 'sonarqube-api'
                    QualityGateStatus(SonarQubecredentialsId)
                }
            }
        }
        stage ('Maven Build: build') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnBuild()
                }
            }
        }
        stage ('Docker image build') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerBuild("${params.ImageName}", "${params.ImageTag}", "${params.DockerHubUser}")
                }
            }
        }
        stage ('Docker Image Scan: trivy') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerImageScan("${params.ImageName}", "${params.ImageTag}", "${params.DockerHubUser}")
                }
            }
        }
    }
}
