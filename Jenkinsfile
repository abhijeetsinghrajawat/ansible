pipeline {
    agent any
    environment {
        PEM_FILE = credentials('ansible-pem-file') // Use the credentials plugin to handle the PEM file
    }
    stages {
        stage('Checkout SCM') {
            steps {
                // Checkout the repository containing the playbook and inventory
                checkout scm
            }
        }
        stage('Prepare Ansible Inventory') {
            steps {
                script {
                    // Save the PEM file to a known location and set appropriate permissions
                    sh 'cp $PEM_FILE /tmp/my-ansible-key.pem'
                    sh 'chmod 600 /tmp/my-ansible-key.pem'

                    // Add the server's SSH key to known hosts to avoid host key verification errors
                    sh '''
                    mkdir -p ~/.ssh
                    ssh-keyscan -H 15.206.149.52 >> ~/.ssh/known_hosts
                    '''

                    // Create the Ansible inventory file
                    writeFile file: 'inventory', text: '''
                    [all]
                    ubuntu_vm ansible_host=15.206.149.52 ansible_user=ubuntu ansible_ssh_private_key_file=/tmp/my-ansible-key.pem
                    '''
                }
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                script {
                    // Ensure Ansible is installed
                    sh '''
                    if ! command -v ansible-playbook &> /dev/null
                    then
                        sudo apt update
                        sudo apt install -y ansible
                    fi
                    '''

                    // Run the Ansible playbook
                    sh 'ansible-playbook -i inventory playbook.yml'
                }
            }
        }
    }
    post {
        always {
            script {
                // Clean up the PEM file
                sh 'rm -f /tmp/my-ansible-key.pem'
            }
        }
    }
}
