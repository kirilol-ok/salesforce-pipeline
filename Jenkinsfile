pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    parameters {
        string(
            name: 'SOURCE_BRANCH',
            defaultValue: 'feature/test-delta',
            description: 'PR source branch'
        )
        
        string(
            name: 'TARGET_BRANCH',
            defaultValue: 'main',
            description: 'PR target branch'
        )
    }

    stages {
        
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/${params.SOURCE_BRANCH}"]],
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
                            --sfdx-url-stdin -\
                            --alias salesforce-pipeline
                    '''
                }
            }
        }

        stage('Create Delta') {
            steps {
                sh '''
                    rm -rf delta
                    mkdir -p delta

                    sf sgd source delta \
                        --from "origin/${TARGET_BRANCH}" \
                        --to "HEAD" \
                        --merge-base \
                        --output-dir delta
                '''
            }
        }

        stage('Show Delta') {
            steps {
                sh '''
                    echo "PACKAGE.XML"
                    cat delta/package.xml

                    echo "DESTRUCTIVE CHANGES"
                    cat delta/destructiveChanges/destructiveChanges.xml || true
                '''
            }
        }

        stage('Verify Salesforce Connection') {
            steps {
                sh 'sf org display --target-org salesforce-pipeline'
            }
        }

        // stage('Deploy') {
        //     steps {
        //         echo "Deploying branch: ${params.BRANCH_NAME}"

        //         sh '''
        //             sf project deploy start \
        //                 --target-org salesforce-pipeline \
        //                 --wait 30
        //         '''
        //     }
        // }
    }
}