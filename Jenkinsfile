@Library('jenkins-shared-library') _
def cfg 
pipeline {
    agent any
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Load Config') {
            steps {
                script {
                    def cfg = readYaml file: 'config.yml'
                    echo "Loaded configuration for ${cfg.ENVIRONMENT}"
                }
            }
        }

        stage('Deploy via Ansible') {
            steps {
                script {
                    k8sAnsibleDeploy(cfg)
                }
            }
        }
    }
    post {
        failure {
            slackSend(
                channel: cfg.SLACK_CHANNEL_NAME.startsWith('#') ? cfg.SLACK_CHANNEL_NAME : "#${cfg.SLACK_CHANNEL_NAME}",
                color: 'danger',
                botUser: true,
                tokenCredentialId: cfg.SLACK_CREDENTIAL_ID,
                message: "Deployment failed for ${cfg.ENVIRONMENT}"
            )
        }
    }
}
