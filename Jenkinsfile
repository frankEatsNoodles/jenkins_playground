pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }
    
    tools {
        jdk 'jdk21'
        maven 'maven3'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                sh 'git fetch --tags --force'
            }
        }

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

        stage('Check Docker') {
            steps {
                sh '''
                docker version
                docker ps
                '''
            }
        }

        stage('Determine Version') {
            steps {
                script {
                    def latestTag = sh(
                        script: "git describe --tags --abbrev=0 2>/dev/null || echo v0.0.0",
                        returnStdout: true
                    ).trim()

                    def version = latestTag.replaceFirst(/^v/, '')
                    def parts = version.tokenize('.')

                    def major = parts[0].toInteger()
                    def minor = parts[1].toInteger()
                    def patch = parts[2].toInteger() + 1

                    env.NEW_VERSION = "v${major}.${minor}.${patch}"

                    echo "Latest tag: ${latestTag}"
                    echo "New version: ${env.NEW_VERSION}"
                }
            }
        }

        stage('Create Git Tag') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'github-jenkins',
                        usernameVariable: 'GITHUB_USERNAME',
                        passwordVariable: 'GITHUB_TOKEN'
                    )
                ]) {
                    sh '''
                        git config user.name "Jenkins"
                        git config user.email "jenkins@localhost"

                        git tag "$NEW_VERSION"

                        git push https://${GITHUB_USERNAME}:${GITHUB_TOKEN}@github.com/frankEatsNoodles/jenkins_playground.git "$NEW_VERSION"
                    '''
                }
            }
        }

        stage('Build and Notify') {
            steps {
                script {
                    emailext (
                        subject: "HI KEVIN",
                        body: "rice rice rice",
                        to: 'kevthekat888@gmail.com'
                    )
                }
            }
        }
    }

    post {
        success {
            emailext (
                subject: "Build Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """Good news. Your build succeeded.

Job: ${env.JOB_NAME}
Build: ${env.BUILD_NUMBER}
Version: ${env.NEW_VERSION}
URL: ${env.BUILD_URL}""",
                to: "56frankwu@gmail.com,joycew.pro@gmail.com"
            )
        }

        failure {
            emailext (
                subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """Your build failed.

Check details here: ${env.BUILD_URL}""",
                to: "56frankwu@gmail.com,joycew.pro@gmail.com"
            )
        }
    }
}
