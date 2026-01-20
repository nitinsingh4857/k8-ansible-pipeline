@Library('jenkins-shared-library') _

pipeline {
    agent any

    environment {
        SLACK_CHANNEL_NAME = 'build-status'
        ENVIRONMENT       = 'prod'
        CODE_BASE_PATH    = 'env/prod'
        ACTION_MESSAGE    = 'Deploying application'
        KEEP_APPROVAL_STAGE = 'true'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Load Configuration') {
            steps {
                script {
                    cfg = readYaml file: 'config.yml'
                    echo "Loaded configuration for ${cfg.ENVIRONMENT}"
                }
            }
        }

        stage('Clone') {
            steps {
                script {
                    echo "Repository already checked out by Jenkins"
                }
            }
        }

        stage('User Approval') {
            steps {
                script {
                    input message: "Approve deployment to ${cfg.ENVIRONMENT}?", ok: "Deploy"
                }
            }
        }

        stage('Ansible Playbook Execution') {
            steps {
                // ✅ Here we set the kubeconfig environment variable
                script {
                    withEnv(["K8S_AUTH_KUBECONFIG=/var/lib/jenkins/.kube/config"]) {
                        k8sAnsibleDeploy(cfg)
                    }
                }
            }
        }
    }

    post {
        success {
            slackSend(channel: "${SLACK_CHANNEL_NAME}", color: 'good', message: "Deployment to ${ENVIRONMENT} succeeded")
        }
        failure {
            slackSend(channel: "${SLACK_CHANNEL_NAME}", color: 'danger', message: "Deployment to ${ENVIRONMENT} failed")
        }
    }
}
