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
        NVD_API_KEY = '<YOUR_NVD_API_KEY>'  // Inject your NVD API key here
    }
    stages {
        stage('Build') {
            steps {
                container('maven') {
                    sh 'mvn clean package'
                }
            }
        }
        stage('Dependency Check') {
            steps {
                container('maven') {
                    sh 'mvn org.owasp:dependency-check-maven:6.1.1:check'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                container('kaniko') {
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
