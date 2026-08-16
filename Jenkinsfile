pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    parameters {
        string(
            name: 'BRANCH_NAME',
            defaultValue: 'main',
            description: 'Git branch to deploy'
        )
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/${params.BRANCH_NAME}"]],
                    userRemoteConfigs: [[
                        url: 'git@github.com:kirilo1-ok/NEW-REPO.git',
                        credentialsId: 'github-ssh'
                    ]]
                ])
            }
        }

        stage('Check Salesforce CLI') {
            steps {
                sh 'sf --version'
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying branch: ${params.BRANCH_NAME}"
            }
        }
    }
}