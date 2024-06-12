pipeline {
    agent {
        label 'ansible-agent' // Use a label to specify the agent with Ansible installed
    }
    environment {
        PEM_FILE = credentials('my-pem-file')
    }
    stages {
        stage('Prepare Ansible Inventory') {
            steps {
                // Save the PEM file to a known location
                sh 'cp $PEM_FILE /tmp/my-ansible-key.pem'
                sh 'chmod 600 /tmp/my-ansible-key.pem'

                // Create Ansible inventory file
                writeFile file: 'hosts.ini', text: '''
                [all]
                ubuntu_vm ansible_host=65.2.172.22 ansible_user=ubuntu ansible_ssh_private_key_file=/tmp/my-ansible-key.pem
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
