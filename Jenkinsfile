pipeline {
    agent any
    options {
        timestamps()
        ansiColor('xterm')
        timeout(time: 45, unit: 'MINUTES')
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
 
    stages {
        stage('Подготовка окружения') {
            steps {
                withCredentials([
                    sshUserPrivateKey(credentialsId: 'vm-ssh-key', keyFileVariable: 'SSH_KEY_FILE'),
                    file(credentialsId: 'ansible-vault-pass', variable: 'ANSIBLE_VAULT_PASSWORD_FILE')
                ]) {
                    dir('ansible') {
                        ansiblePlaybook(
                            installation: 'ansible',
                            playbook: 'playbook.yml',
                            inventory: 'inventory/stend1/hosts',
                            tags: 'docker,clone',
                            extraVars: [ansible_ssh_private_key_file: "${SSH_KEY_FILE}"],
                            colorized: true
                        )
                    }
                }
            }
        }
 
        stage('Запуск микросервисов') {
            steps {
                withCredentials([
                    sshUserPrivateKey(credentialsId: 'vm-ssh-key', keyFileVariable: 'SSH_KEY_FILE'),
                    file(credentialsId: 'ansible-vault-pass', variable: 'ANSIBLE_VAULT_PASSWORD_FILE')
                ]) {
                    dir('ansible') {
                        ansiblePlaybook(
                            installation: 'ansible',
                            playbook: 'playbook.yml',
                            inventory: 'inventory/stend1/hosts',
                            tags: 'up',
                            extraVars: [ansible_ssh_private_key_file: "${SSH_KEY_FILE}"],
                            colorized: true
                        )
                    }
                }
            }
        }
    }
 
    post {
        success {
            echo 'Микросервисы развернуты и запущены. Сайт: http://192.168.0.103:8080'
        }
        failure {
            echo 'Пайплайн завершился с ошибкой - вывод в Console Output.'
        }
    }
}