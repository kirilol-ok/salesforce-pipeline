pipeline {
    agent any

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
                git branch: "${params.BRANCH_NAME}",
                    credentialsId: 'github-ssh',
                    url: 'git@github.com:kirilol-ok/salesforce-pipeline.git'
            }
        }
    }
}