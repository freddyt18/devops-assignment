pipeline {
    agent {
        label 'main'
    }
    environment {
        WORKSPACE_DIR = '/var/lib/jenkins/workspace/DevOps/jenkins_infra/'
        PLAYBOOK_DIR = 'Ansible'
    }
    stages {
        stage('Installing Tools on main server') {
            steps {
                sh """
                    cd ${WORKSPACE_DIR} && \\
                    cd ${PLAYBOOK_DIR} && \\
                    ansible-playbook playbooks/01_tools.yml -e "host=localhost"
                """
            }
        }

        stage('Create a Droplet and Point the domain to the Droplet') {
            steps {
                sh """
                    cd ${WORKSPACE_DIR} && \\
                    cd ${PLAYBOOK_DIR} && \\
                    ansible-playbook playbooks/02_create_droplet.yml -e "digital_ocean_token=${digital_ocean_token} cloudflare_email=${cloudflare_email} cloudflare_api_token=${cloudflare_api_token}"
                """

                sh '''
                    rm -rf ~/tmp_droplet && \\
                    cd ~ && \\
                    mkdir tmp_droplet && \\
                    cd tmp_droplet && \\
                    
                    git clone https://github.com/freddyt18/devops-assignment.git && \\
                    cp ~/workspace/DevOps/jenkins_infra/Ansible/playbooks/hosts ~/tmp_droplet/devops-assignment/Ansible/playbooks/hosts && \\
                    cd devops-assignment/Ansible && \\
                    
                    git add . && \\
                    git commit -m "Added the droplet IP" && \\
                    git push origin master && \\
                    
                    rm -rf ~/tmp_droplet
                '''
            }
        }

        stage('Install Docker, Docker Compose, and Uptime Kuma on the Droplet and Configure Nginx') {
            steps {
                sh """

                    rm -rf ~/tmp_ && \\
                    mkdir ~/tmp_ && \\
                    cd ~/tmp_ && \\

                    git clone https://github.com/freddyt18/devops-assignment.git && \\
                    cd devops-assignment/Ansible && \\

                    ansible-playbook playbooks/01_tools.yml -e "host=droplet" && \\

                    cd ~/tmp_/devops-assignment/Ansible && \\
                    ansible-playbook playbooks/03_uptime_kuma_nginx.yml -e "devops_main=${devops_main} devops_ip=${devops_ip}" && \\

                    rm -rf ~/tmp_
                """
            }
        }
    }
}
