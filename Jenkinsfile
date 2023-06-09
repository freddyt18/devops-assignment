/* groovylint-disable LineLength */
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
                    ansible-playbook playbooks/01_tools.yml -e "host=localhost" -vvv
                """
            }
        }

        stage('Create a Droplet and Point the domain to the Droplet') {
            steps {
                sh """
                    cd ${WORKSPACE_DIR} && \\
                    cd ${PLAYBOOK_DIR} && \\
                    ansible-playbook playbooks/02_create_droplet.yml -e "digital_ocean_token=${digital_ocean_token} cloudflare_email=${cloudflare_email} cloudflare_api_token=${cloudflare_api_token} uptime_domain=${uptime_domain}" -vvv
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

                    ansible-playbook playbooks/01_tools.yml -e "host=droplet" -vvv && \\

                    cd ~/tmp_/devops-assignment/Ansible && \\
                    ansible-playbook playbooks/03_uptime_kuma_nginx.yml -e "devops_main=${devops_main} devops_ip=${devops_ip} uptime_domain=${uptime_domain}" && \\

                    rm -rf ~/tmp_
                """
            }
        }

        stage('Setting up Kubernetes cluster with the created Droplet'){
            steps {
                sh """
                    rm -rf ~/tmp_ && \\
                    mkdir ~/tmp_ && \\
                    cd ~/tmp_ && \\

                    git clone https://github.com/freddyt18/devops-assignment.git && \\
                    cd devops-assignment/Ansible && \\

                    ansible-playbook playbooks/04_cluster.yml -e "digital_ocean_token=${digital_ocean_token}" -vvv && \\

                    rm -rf ~/tmp_
                    
                """
            }
        }
    }

    // Post stage
    // Send message if failed or success
    post {
        success {
            sh """
                sudo curl -X POST "https://api.telegram.org/bot${telegram_token}/sendMessage" -d "chat_id=642027926&text=Infrastructure%20has%20been%20deployed%20successfully."
            """
        }
        failure {
            sh """
                sudo curl -X POST "https://api.telegram.org/bot${telegram_token}/sendMessage" -d "chat_id=642027926&text=Infrastructure%20has%20failed%20to%20deploy."
            """
        }
        
    }

}
