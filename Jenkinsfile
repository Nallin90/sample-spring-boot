pipeline {
        agent none
            environment {
            ENV_DOCKER = credentials('dockerhub')
            DOCKERIMAGE = "dummy/dummy"
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
                    sh 'echo docker build'
                    dockerImage = docker.build("baronea90/coglab:$BUILD_NUMBER")
                }
            }
            stage('docker push') {
                steps {
                    script {
                        docker.withRegistry( 'https://registry.hub.docker.com', credentials ) {
                            dockerImage.push("$BUILD_NUMBER")
                            dockerImage.push('latest')
                        }
                    }
                }
            stage('Deploy App') {
                steps {
                    sh 'echo deploy to kubernetes'
                }
        }
}
