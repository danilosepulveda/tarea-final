pipeline {
    agent {
        kubernetes {
            defaultContainer 'node-tool'
            yamlFile 'agent.yaml'
        }
    }

    environment {
        DH_REPO = 'danilosepulvedaf/tarea-final'
        GH_REPO = 'ghcr.io/danilosepulveda/tarea-final'
        K8S_NAMESPACE = 'ns-danilo-sepulveda'
    }

    stages {
        stage('install') {
            steps {
                sh 'corepack enable'
                sh 'pnpm install --frozen-lockfile'
            }
        }

        stage('test') {
            steps {
                sh 'pnpm build'
            }
        }

        stage('push') {
            steps {
                container('buildkit') {
                    sh '''
                        export DOCKER_CONFIG=/docker-config/dockerhub
                        test -s ${DOCKER_CONFIG}/config.json

                        buildctl-daemonless.sh build --frontend dockerfile.v0 --local context=. --local dockerfile=. --output type=image,\"name=${DH_REPO}:danilo-sepulveda\",push=true

                        export DOCKER_CONFIG=/docker-config/github
                        test -s ${DOCKER_CONFIG}/config.json

                        buildctl-daemonless.sh build frontend dockerfile.v0 --local context=. --local dockerfile=. --output type=image,\"name=${GH_REPO}:danilo-sepulveda\",push=true
                        '''
                }
            }
        }
        stage('deploy') {
            steps {
                container('kubectl-tool') {
                    withKubeConfig([credentialsId: 'kubernetes-config']) {
                        sh '''
                            kubectl apply -f entrega.yaml
                            kubectl rollout status deployment/app-danilo-sepulveda - ${K8S_NAMESPACE}
                            '''
                    }
                }
            }
        }
    }
}