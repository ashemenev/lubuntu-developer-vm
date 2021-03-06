pipeline {
    agent any 
    environment {
        PACKET_PROJECT_NAME = 'lubuntu-developer-vm'
        PACKET_PROJECT_ID = 'bc6f3827-e34c-42f8-b4eb-064293c3457c'
        PACKET_HOSTNAME = 'lubuntu-developer-vm'
        VM_UPLOAD_OPTIONS = '--ftp-ssl'
    }
    stages {
        stage('Setup Build') { 
            steps {
                sh "ansible --version"
                sh "if [ -d vagrant-packet-builder ]; then rm -Rf vagrant-packet-builder; fi"
                sh "git clone https://github.com/fuinorg/vagrant-packet-builder.git"
            }
        }
        stage('Build Images') { 
            steps {
                dir('vagrant-packet-builder') {
                    withCredentials( [ 
                            string(credentialsId: 'packet-lubuntu-developer-vm-api-token', variable: 'PACKET_API_TOKEN'), 
                            string(credentialsId: 'fuin_vm_upload_url', variable: 'VM_UPLOAD_URL'),
                            string(credentialsId: 'fuin_vm_upload_user', variable: 'VM_UPLOAD_USER'),
                            string(credentialsId: 'fuin_vm_upload_pw', variable: 'VM_UPLOAD_PW'),
                            string(credentialsId: 'vmware_workstation_14_linux_serial_number', variable: 'VMWARE_SERIAL'),
                            string(credentialsId: 'vmware_workstation_14_linux_bundle_url', variable: 'VMWARE_URL'),
                            string(credentialsId: 'vagrant_vmware_lic_url', variable: 'VAGRANT_VMW_LIC_URL')                        
                                     ] ) {
                        script {
                            sh """
                            ansible-playbook playbook-create-vm.yaml ${params.SKIP_TAGS} --extra-vars '{ "git_project_url": "https://github.com/fuinorg/lubuntu-developer-vm.git", "git_project_key": "None", "vm_upload_url": "${VM_UPLOAD_URL}", "packet_project_name": "${PACKET_PROJECT_NAME}", "packet_project_id": "${PACKET_PROJECT_ID}", "packet_plan": "baremetal_0", "packet_facility": "ams1", "packet_operating_system": "ubuntu_18_04", "packet_hostname": "${PACKET_HOSTNAME}-${env.BUILD_NUMBER}", "packet_api_token": "${PACKET_API_TOKEN}", "ansible_ssh_private_key_file": "/private/jenkins/vagrant-base-box-builder_id_rsa", "packet_key_file": "/private/jenkins/vagrant-base-box-builder_id_rsa.pub", "vmware_serialNumber": "${VMWARE_SERIAL}", "vmware_url": "${VMWARE_URL}", "vagrant_vmware_lic_url": "${VAGRANT_VMW_LIC_URL}", "vm_name_vbx": "lubuntu_dev_vbx", "vm_name_vmw": "lubuntu_dev_vmw", "vm_upload_url": "${VM_UPLOAD_URL}", "vm_upload_user": "${VM_UPLOAD_USER}", "vm_upload_pw": "${VM_UPLOAD_PW}", "vm_upload_options": "${VM_UPLOAD_OPTIONS}", "artifact_version": "${VM_VERSION}" }'
                            """
                        }
                    }
                }
            }
        }
    }
}
