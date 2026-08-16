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
                        url: 'git@github.com:kirilol-ok/salesforce-pipeline.git',
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

        stage('Authenticate Salesforce') {
            steps {
                withCredentials([
                    string(
                        credentialsId: 'salesforce-auth-url',
                        variable: 'SFDX_AUTH_URL'
                    )
                ]) {
                    sh '''
                        printf '%s' "$SFDX_AUTH_URL" |
                        sf org login sfdx-url \
                            --sfdx-url-stdin \
                            --alias target-org
                    '''
                }
            }
        }

        stage('Verify Salesforce Connection') {
            steps {
                sh 'sf org display --target-org salesforce-pipeline'
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying branch: ${params.BRANCH_NAME}"
            }
        }
    }
}