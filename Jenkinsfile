node {
	stage ('Checkout Repository') {
		// Get our repo cloned and prepped for action
		deleteDir()
		checkout scm
	}

        stage ('Setup Environment') {
		// Setup our python and ansible environment
		sh 'python3 -m venv jenkins_build'
		sh 'jenkins_build/bin/python -m pip install -r requirements.txt'
		sh 'git clone https://github.com/carlniger/napalm-ansible'
		sh 'cp -r napalm-ansible/napalm_ansible/ jenkins_build/lib/python3.6/site-packages/'
		sh 'jenkins_build/bin/python napalm-ansible/setup.py install'
		sh '''sed -i -e 's/\\/usr\\/local/jenkins_build/g' ansible.cfg'''
		sh '''sed -i -e 's/dist-/site-/g' ansible.cfg'''
		sh '''sed -i -e 's/\\/usr\\/bin/jenkins_build\\/bin/g' ansible.cfg'''
	}

	stage ('Validate Generate Configurations Playbook') {
		// Validate the syntax in our generate configurations Playbook
		sh 'ansible-playbook generate_configurations.yaml -e "ansible_python_interpreter=jenkins_build/bin/python" --syntax-check'
	}


	stage ('Render Configurations') {
		// Generate our configurations with our sweet Playbooks
		sh 'ansible-playbook generate_configurations.yaml -e "ansible_python_interpreter=jenkins_build/bin/python"'
	}

        stage ('Validate Deploy Configurations Playbook') {
                // Validate the syntax in our deploy configurations Playbook 
                sh 'ansible-playbook deploy_configurations.yaml -e "ansible_python_interpreter=jenkins_build/bin/python" --syntax-check'
        }
	
	stage ('Deploy Configurations to Dev') {
		sh 'ansible-playbook deploy_configurations.yaml -e "ansible_python_interpreter=jenkins_build/bin/python"'
	}

	stage ('Functional/Integratoin Testing') {
		// Ping stuff and make sure we didn't blow up dev!
	}

	stage ('Promot Configurations to Production') {
		// Push the configuratoins out to the prod environment
	}

	stage('Production Functional/Integration Testing') {
		// Ping stuff and make sure we didn't blow up prod!
	}
}
