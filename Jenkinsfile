pipeline {
    agent any

    options {
        timeout(time: 30, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10', daysToKeepStr: '30'))
        timestamps()
        ansiColor('xterm')
    }

    parameters {
        string(name: 'DEPLOY_ENV', defaultValue: 'dev', description: 'Deployment environment (dev/stage/prod)')
    }

    environment {
        JAVA_HOME = tool name: 'jdk11', type: 'jdk'
        MAVEN_HOME = tool name: 'M3', type: 'maven'
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"
        NOTIFY_EMAIL = 'devops-team@example.com'
        BRANCH_NAME = "${env.GIT_BRANCH ?: 'master'}"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Clean workspace before checkout
                    deleteDir()
                }
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/${BRANCH_NAME}']],
                    userRemoteConfigs: [[url: 'https://github.com/asifjoardar/hello-jenkins.git']]
                ])
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'maven:3.8.8-openjdk-11'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            steps {
                script {
                    sh 'mvn clean compile -B'
                }
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'maven:3.8.8-openjdk-11'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            steps {
                script {
                    sh 'mvn test -B'
                }
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Analysis') {
            agent {
                docker {
                    image 'maven:3.8.8-openjdk-11'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            steps {
                script {
                    // Placeholder for static analysis, e.g., SpotBugs or SonarQube
                    sh 'mvn verify -B'
                }
            }
        }

        stage('Deploy') {
            when {
                branch 'master'
            }
            steps {
                script {
                    // Placeholder for deployment logic
                    echo "Deploying to ${params.DEPLOY_ENV} environment..."
                    // Example: sh './deploy.sh ${params.DEPLOY_ENV}'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            mail to: "${env.NOTIFY_EMAIL}",
                 subject: "SUCCESS: Jenkins Build #${env.BUILD_NUMBER} for ${env.JOB_NAME}",
                 body: "Build succeeded!\n\nJob: ${env.JOB_NAME}\nBuild: #${env.BUILD_NUMBER}\nBranch: ${env.BRANCH_NAME}\nCheck console output at ${env.BUILD_URL}"
        }
        failure {
            mail to: "${env.NOTIFY_EMAIL}",
                 subject: "FAILURE: Jenkins Build #${env.BUILD_NUMBER} for ${env.JOB_NAME}",
                 body: "Build failed.\n\nJob: ${env.JOB_NAME}\nBuild: #${env.BUILD_NUMBER}\nBranch: ${env.BRANCH_NAME}\nCheck console output at ${env.BUILD_URL}"
        }
        unstable {
            mail to: "${env.NOTIFY_EMAIL}",
                 subject: "UNSTABLE: Jenkins Build #${env.BUILD_NUMBER} for ${env.JOB_NAME}",
                 body: "Build is unstable.\n\nJob: ${env.JOB_NAME}\nBuild: #${env.BUILD_NUMBER}\nBranch: ${env.BRANCH_NAME}\nCheck console output at ${env.BUILD_URL}"
        }
        cleanup {
            // Additional cleanup logic if needed
        }
    }
}