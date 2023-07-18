pipeline {
    agent {
        node {
          label 'master'
        }
    }
    options {
        ansiColor('xterm')
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
                ansible-playbook -i /etc/ansible/hosts ${WORKSPACE}/ans/launch_EC2_ANS.yaml --extra-vars "$EXTRA_VARS workdir=$WORKSPACE/$stack_name.txt template=$WORKSPACE/$template"
                '''
	     }
        }
        stage('Initialize new server'){
            steps {
                sh '''
                cd $WORKSPACE
                new_public_ip=`cat ${stack_name}.txt | grep -w "PublicIP" | awk -F":" '{ print $2 }' | awk -F'"' '{ print $2 }'`
                echo $new_public_ip
                ansible-playbook -i /etc/ansible/hosts ${WORKSPACE}/ans/init_new_server.yaml --extra-vars "server=$server new_public_ip=$new_public_ip"
                '''
            }
        }
        stage('execute tasks on new server'){
            steps {
                sh '''
                cd $WORKSPACE
                new_public_ip=`cat ${stack_name}.txt | grep -w "PublicIP" | awk -F":" '{ print $2 }' | awk -F'"' '{ print $2 }'`
                echo $new_public_ip
                ansible-playbook -i /etc/ansible/hosts ${WORKSPACE}/ans/create_user.yaml --extra-vars "new_public_ip=$new_public_ip"
                '''
            }
        }
    }
}
