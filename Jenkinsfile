def AUTFailed = false

pipeline {
	agent any

	//Parameters Required at the start of the Build
	parameters {
		string(defaultValue: 'Name of the Branch', 
			description: 'pega rule branch', 
			name: 'branch_name')
		
		string(defaultValue: 'pxInsName of Test Suite', 
			description: 'Please enter the pxInsName of the Test Suite.', 
			name: 'test_suite_id')
		
		string(defaultValue: 'CICDSourceProduct1', 
			description: 'The name of the RAP used to generate the archive.', 
			name: 'productName')
		
		string(defaultValue: '01.01.01', 
			description: 'The Version of the RAP used to generate the archive.', 
			name: 'productVersion')
			
		choice(choices: 'http://srvawsbpmldbvm1:8090/', 
			description: 'URL of Source Host', 
			name: 'SourceHost')
			
		choice(choices: 'http://srvawsbpmlwbvm1:8080', 
			description: 'URL of Target Host', 
			name: 'TargetHost')		
	}

	//Stages of Pipeline
	stages {
		
		


		stage('Deploy to L2') {
			steps {
				
				load "${WORKSPACE}/loadprop.properties"
				
				
			}
		}



		
	}


	
}

