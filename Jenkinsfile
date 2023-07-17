pipeline {
    agent {
        node {
          label 'master'
        }
    }
    stages {
        stage('Checkout code from SCM') {
			steps{
				cleanWs()
				
				checkout([$class: 'GitSCM',
					branches: [
						[name: "main"]
					],
					doGenerateSubmoduleConfigurations: false,
					extensions: [],
					gitTool: 'Default',
					submoduleCfg: [],
					userRemoteConfigs: [
						[credentialsId: 'Node-Auth',
							url: '${git_url}'
						
						]
					]
				])
			}
		}
		stage('Launch CFT') {
            steps {
                sh '''
                whoami
		// EXTRA_VARS
		// server=$server stack_name=$stack_name region=$region UserID=$UserID InstanceName=$InstanceName InstanceType=$InstanceType SecurityGroupIds=$SecurityGroupIds ImageID=$ImageID SubnetID=$SubnetID
                ansible-playbook -i /etc/ansible/hosts ${WORKSPACE}/ans/launch_EC2_ANS.yaml --extra-vars "$EXTRA_VARS template=$WORKSPACE/$template"
                '''
            }
        }
    }
}
