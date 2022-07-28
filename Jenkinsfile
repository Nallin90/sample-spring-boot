pipeline {
        agent none
            environment {
            ENV_DOCKER = credentials('Docker')
            DOCKERIMAGE = "baronea90/coglab"
            EKS_CLUSTER_NAME = "demo-cluster"
        }
        stages {
            stage('build') {
                agent {
                    docker { image 'openjdk:11-jdk' }
                }
                steps {
                    sh 'chmod +x gradlew && ./gradlew build jacocoTestReport'
                }
            }
            stage('sonarqube') {
            agent {
                docker { image 'sonarsource/sonar-scanner-cli:latest' } }
                steps {
                    sh 'echo scanning!'
                }
            }
            stage('docker build') {
                steps {
                    script{
                        sh 'echo docker build'
                        dockerImage = docker.build("$DOCKERIMAGE:$BUILD_NUMBER")
                    }
                }
            }
            stage('docker push') {
            agent any
                steps {
                    script {
                        docker.withRegistry( 'https://registry.hub.docker.com', credentials ) {
                            dockerImage.push("$BUILD_NUMBER")
                            dockerImage.push('latest')
                        }
                    }
                }
            }
            stage('Deploy App') {
            agent any
                steps {
                        withKubeConfig(caCertificate: '', clusterName: 'tufail-sre', contextName: '', credentialsId: 'K8S', namespace: '', serverUrl: '') {
                            sh ('kubectl apply -f' )
                        }
                }
            }
        }
}
