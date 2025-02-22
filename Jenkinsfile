pipeline {
    agent { label 'jappbuildserver1' }    

    tools {
        // Install the Maven version configured as "maven" and add it to the path.
        maven "maven"
    }

    environment {    
        DOCKERHUB_CREDENTIALS = credentials('dockerloginid')
    } 
    
    stages {
        stage('SCM Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                    userRemoteConfigs: [[url: 'https://github.com/cloudpost03/BankingApp.git']], 
                    extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'BankingApp']]
                ])
            }
        }
        stage('Maven Build') {
            steps {
                dir('BankingApp') {
                    sh "mvn -Dmaven.test.failure.ignore=true clean package"
                }
            }
        }
        stage('Docker Build') {
            steps {
                dir('BankingApp') {
                    sh 'docker build -t pravinkr11/bankingapp:1.0 .'
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub_cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh 'docker push pravinkr11/bankingapp:1.0'
                    }
                }
            }
        }
        stage('Deploy to Kubernetes Dev Environment') {
            steps {
                script {
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: 'Kubernetes', 
                                transfers: [
                                    sshTransfer(
                                        cleanRemote: false, 
                                        excludes: '', 
                                        execCommand: 'kubectl apply -f kubernetesdeploy.yaml', 
                                        execTimeout: 120000, 
                                        flatten: false, 
                                        makeEmptyDirs: false, 
                                        noDefaultExcludes: false, 
                                        patternSeparator: '[, ]+', 
                                        remoteDirectory: '.', 
                                        remoteDirectorySDF: false, 
                                        removePrefix: '', 
                                        sourceFiles: '*.yaml'
                                    )
                                ], 
                                usePromotionTimestamp: false, 
                                useWorkspaceInPromotion: false, 
                                verbose: false
                            )
                        ]
                    )
                }
            }
        }
    }
}
