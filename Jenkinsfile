node {
	stage ('Checkout Repository') {
		// Get our repo cloned and prepped for action
		deleteDir()
		checkout scm
	}

	stage ('Render Configurations') {
		// Generate our configurations with our sweet Playbooks
		sh 'ansible-playbook generate_configurations.yaml'
	}
	
	stage ('Unit Testing') {
		// Do some kind of "linting" on our code to make sure we didn't bugger anything up too badly
	}

	stage ('Deploy Configurations to Dev') {
		// Push the configurations out to the dev environment
		sh 'python3 -m venv jenkins_build'
		sh 'jenkins_build/bin/python -m pip install -r requirements.txt'
		sh 'git clone https://github.com/carlniger/napalm-ansible'
		sh 'jenkins_build/bin/python napalm-ansible/setup.py install'
		sh '''sed -i -e 's/\\/usr\\/local/jenkins_build/g' ansible.cfg'''
		sh '''sed -i -e 's/dist-/site-/g' ansible.cfg'''
		sh '''ansible-playbook deploy_configurations.yaml -e "ansible_python_interpreter=jenkins_build/bin/python"'''
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
