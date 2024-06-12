pipeline {
    agent {
        docker {
            image 'ansible/ansible:latest' // Use a Docker image with Ansible installed
            args '-v /tmp:/tmp' // Mount the /tmp directory to access the PEM file
        }
    }
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
                [ubuntu]
                your_ubuntu_vm ansible_host=your_vm_ip ansible_user=your_vm_user ansible_ssh_private_key_file=/tmp/my-ansible-key.pem
                '''
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                // Run the Ansible playbook
                sh 'ansible-playbook -i hosts.ini create_file.yml'
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
