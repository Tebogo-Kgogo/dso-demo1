pipeline {
    agent {
        kubernetes {
            yamlFile 'build-agent.yaml'
            defaultContainer 'maven'
        }
    }
    environment {
        DOCKER_REGISTRY = 'docker.io/xxxxxx'
        IMAGE_NAME = 'dso-demo'
    }
    stages {
        stage('Build') {
            container('maven') {
                steps {
                    sh 'mvn clean package'
                }
            }
        }
        stage('Dependency Check') {
            container('maven') {
                steps {
                    sh 'mvn org.owasp:dependency-check-maven:6.1.1:check'
                }
            }
        }
        stage('Build Docker Image') {
            container('kaniko') {
                steps {
                    sh '''
                    /kaniko/executor \
                        -f $WORKSPACE/Dockerfile \
                        -c $WORKSPACE \
                        --insecure \
                        --skip-tls-verify \
                        --cache=true \
                        --destination=$DOCKER_REGISTRY/$IMAGE_NAME \
                        --force
                    '''
                }
            }
        }
    }
}
