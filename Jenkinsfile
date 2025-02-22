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
        dir('star-agile-insurance-project') {
            sh 'sudo docker build -t pravinkr11/insuranceproject:1.0 .'
        }
    }
}
stage('Docker Push') {
    steps {
        dir('star-agile-insurance-project') {
            sh 'echo $dockerhub_cred_PSW | sudo docker login -u $dockerhub_cred_USR --password-stdin'
            sh 'sudo docker push pravinkr11/insuranceproject:1.0'
        }
    }
}
        stage('Docker Push') {
            steps {
                dir('staragile-insurance-project') {
                    sh 'echo $dockerhub_cred_PSW | docker login -u $dockerhub_cred_USR --password-stdin'
                    sh 'docker push pravinkr11/insuranceproject:1.0'
                }
            }
        }
    }
}
