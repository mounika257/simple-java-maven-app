@Library('my-shared-library') _

pipeline{
    agent any 

        parameters{
            choice(name: 'action', choices: 'create\ndelete', description:'choose create/destroy')
            string(name: 'Imagename', description:'name of docker build', defaultValue: 'javaapp')
            string(name: 'ImageTag', description:'tag of docker build', defaultValue: 'v1')
            string(name: 'DockerHubUser', description:'name of App', defaultValue: 'mounika257')
        }

        stages{
            stage ('checkout'){
                when { expression { parameters.action == 'create'}}
                steps{
                    gitCheckout(
                        branch:'main',
                        url: 'https://github.com/mounika257/simple-java-maven-app.git'
                    )
                }
            }
            stage ('maven test'){
                when { expression { parameters.action == 'create'}}
                steps{
                    script{
                    mvnTest()
                    }
                }
            }
            stage ('Integration Test maven'){
                when { expression { parameters.action == 'create'}}
                steps{
                    script{
                    mvnIntegrationTest()
                    }
                }
            }
            stage ('Static code analysis: Sonarqube'){
                when { expression {parameters.action == 'create'}}
                steps{
                    script{
                        def SonarQubecredentialsId = 'sonarqube-api'
                        statiCodeAnalysis(SonarQubecredentialsId)
                    }
                }
            }     
            stage ('Quality Gate Status Check : Sonarqube'){
                when{
                  expression { parameters.action == 'create'}
                }
                steps{
                    script{
                       def SonarQubecredentialsId = 'sonarqube-api'
                       QualityGateStatus(SonarQubecredentialsId)
                    }
                }
            }      
            stage ('Maven Build: build'){
                when { expression {parameters.action == 'create'}}
                steps{
                    script{
                        mvnBuild()
                    }
                }
            }
            stage ('Docker image build'){
                when { expression {parameters.action == 'create'}}
                steps{
                    script{
                        docker build("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
                    }
                }
            } 
            stage ('Docker Image Scan: trivy'){
                when { expression {parameters.action == 'create'}}
                steps{
                    script{
                        dockerImageScan("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
                    }
                }
            }                                                                                                      
            
        }
    
}
