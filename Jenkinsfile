pipeline {
    agent any

    triggers {
        // dispara quando houver push ou pull request (via webhook do GitHub)
        githubPush()
    }

    environment {
        DOCKER_HOST_CREDENTIALS = 'docker-host-ssh'
        DOCKER_HOST = 'ubuntu@docker-server.connect.local'
        REPO_URL = 'git@github.com:JoelFernandes01/docker-compose-zabbix-mysql-grafana-prometheus.git'
        APP_DIR = '~/zabbix-stack'
    }

    stages {
        stage('Checkout') {
            steps {
                sshagent (credentials: [DOCKER_HOST_CREDENTIALS]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DOCKER_HOST} '
                            if [ ! -d ${APP_DIR} ]; then
                                git clone ${REPO_URL} ${APP_DIR}
                            else
                                cd ${APP_DIR} && git pull origin main
                            fi
                        '
                    """
                }
            }
        }

        stage('Deploy Zabbix Stack') {
            steps {
                sshagent (credentials: [DOCKER_HOST_CREDENTIALS]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DOCKER_HOST} '
                            cd ${APP_DIR} &&
                            docker compose pull &&
                            docker compose up -d
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deploy concluído com sucesso!"
        }
        failure {
            echo "❌ Falha no deploy!"
        }
    }
}
