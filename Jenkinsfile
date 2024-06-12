pipeline {
    agent any
    environment {
        PEM_FILE = credentials('my-pem-file') // Use the credentials plugin to handle the PEM file
    }
    stages {
        stage('Checkout SCM') {
            steps {
                // Checkout the repository containing the playbook
                checkout scm
            }
        }
        stage('Prepare Ansible Inventory') {
            steps {
                // Save the PEM file to a known location and set appropriate permissions
                sh 'cp $PEM_FILE /tmp/my-ansible-key.pem'
                sh 'chmod 600 /tmp/my-ansible-key.pem'

                // Create the Ansible inventory file
                writeFile file: 'hosts.ini', text: '''
                [all]
                ubuntu_vm ansible_host=13.201.77.178 ansible_user=ubuntu ansible_ssh_private_key_file=$PEM_FILE
                '''
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                // Run the Ansible playbook
                sh 'ansible-playbook -i hosts.ini playbook.yml'
            }
        }
    }
    post {
        cleanup {
            // Clean up the PEM file
            sh 'rm -f /tmp/my-ansible-key.pem'
        }
    }
}

