@Library('jenkins-shared-library') _

node {
    stage('Load Config') {
        cfg = readYaml file: 'config.yml'
        echo "Loaded configuration for ${cfg.ENVIRONMENT}"
    }

    stage('Deploy via Shared Library') {
        // Call shared library function and pass config
        k8sAnsibleDeploy(cfg)
    }
}
