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
environment
	{
		PATH="C:\\WINDOWS\\SYSTEM32"
	}
		
	//Stages of Pipeline
	stages {
		stage ('Stage1'){
	        steps {
	            script{
	                bat label: '', script: 'echo "00"'
	                   }
	              }
	                         }

		stage('Deploy to L2') {
			steps {
				bat label: '', script: 'echo "00"'
				load "loadprop.properties"
				bat 'echo "0"'
				//bat 'del %WORKSPACE%\\tmp /s /f /q'
				bat 'echo "1"'
				//bat 'mkdir %WORKSPACE%\\tmp'
				bat 'echo "2"'
				bat "copy %WORKSPACE%\\scripts\\samples\\jenkins\\build.xml %WORKSPACE%"
				bat 'echo "3"'
				withAnt(installation: 'Ant') {
					bat "ant exportprops"
					bat "%WORKSPACE%\\scripts\\utils\\prpcServiceUtils.bat export --connPropFile %WORKSPACE%\\scripts\\utils\\%SystemName%_export.properties --artifactsDir %WORKSPACE%"
				}
				bat 'echo "4"'
				withAnt(installation: 'Ant') {
				bat 'echo "5"'
					bat "ant importprops"
					bat 'echo "6"'
					bat "%WORKSPACE%\\scripts\\utils\\prpcServiceUtils.bat import --connPropFile %WORKSPACE%\\scripts\\utils\\%SystemName%_import.properties --artifactsDir %WORKSPACE%"
					bat 'echo "7"'
                           
			      }
		}



		
	}


	
}

}
