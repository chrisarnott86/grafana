pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-chris')
    }

    stages {
        stage('Docker Build') {
            steps {
                container('docker') {
                    sh """
                    cd packaging/docker/custom
                    docker build --build-arg "GRAFANA_VERSION=latest" --build-arg "GF_INSTALL_IMAGE_RENDERER_PLUGIN=true" --build-arg "GF_INSTALL_PLUGINS=grafana-simple-json-datasource,fifemon-graphql-datasource" -t chrisarnott86/grafana-render:latest -f Dockerfile .
                    """
                }
            }
        }
        stage('Docker Login') {
            steps {
                container('docker') {
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                container('docker') {
                    sh 'docker push chrisarnott86/grafana-render:latest'
                }
            }
        }
    }
    post {
        always {
            container('docker') {
                sh 'docker logout'
            }
        }
    }
}
