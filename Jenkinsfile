pipeline {
    agent any
    stages {
        stage('Installing Tools'){
            steps {
                sh '''
                    cd ~/personal/devops-assignment/ && \
                        cd Ansible && \
                        ansible-playbook playbooks/tools.yml
                '''
            }
        }
    }
}
