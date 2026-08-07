pipeline {
    agent any

    tools {
        jdk 'jdk21'
        maven 'maven3'
    }

    stages {
        stage('Java Version') {
            steps {
                sh 'java -version'
            }
        }

        stage('Maven Version') {
            steps {
                sh 'mvn -version'
            }
        }

        stage('Build') {
            steps {
                echo 'Running build...'
            }
        }
        stage('Check Docker') {
            steps {
                sh '''
                docker version
                docker ps
                '''
            }
        }
    }

    post {
        success {
            emailext (
                subject: "Build Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Good news. Your build succeeded.\n\nJob: ${env.JOB_NAME}\nBuild: ${env.BUILD_NUMBER}\nURL: ${env.BUILD_URL}",
                to: "56frankwu@gmail.com,kevthekat888@gmail.com,kevthekat8888@gmail.com"
            )
        }
        failure {
            emailext (
                subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Your build failed.\n\nCheck details here: ${env.BUILD_URL}",
                to: "56frankwu@gmail.com"
            )
        }
    }
}
