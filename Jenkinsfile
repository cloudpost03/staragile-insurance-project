pipeline {
    agent any

    tools {
        maven 'maven'
    }

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '3')
    }

    environment {
        dockerhub_cred = credentials('dockerhub_cred')
    }

    stages {
        stage('Cleanup') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: 'main']],  
                    userRemoteConfigs: [[
                        url: 'https://github.com/cloudpost03/staragile-insurance-project.git',
                        credentialsId: 'git_cred'
                    ]],
                    extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'staragile-insurance-project']]
                ])
            }
        }

        stage('Build') {
            steps {
                dir('staragile-insurance-project') {
                    script {
                        if (fileExists('pom.xml')) {
                            sh 'mvn clean package'
                        } else {
                            error 'pom.xml not found. Verify the repository checkout.'
                        }
                    }
                }
            }  
        }

        stage('Docker Build') {
            steps {
                dir('staragile-insurance-project') {
                    sh 'docker build -t pravinkr11/insuranceproject:1.0 .'
                }
            }
        }

        stage('Docker Push') {
            steps {
                dir('staragile-insurance-project') {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub_cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh '''
                            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                            docker push pravinkr11/insuranceproject:1.0
                        '''
                    }
                }
            }
        }
    }
}
