def AUTFailed = false

pipeline {
	agent any

	//Parameters Required at the start of the Build
	parameters {
		string(defaultValue: 'Name of the Branch', 
			description: '', 
			name: 'branch_name')
		
		string(defaultValue: 'pxInsName of Test Suite', 
			description: 'Please enter the pxInsName of the Test Suite.', 
			name: 'test_suite_id')
		
		string(defaultValue: '', 
			description: 'The name of the RAP used to generate the archive.', 
			name: 'productName')
		
		string(defaultValue: '01.01.01', 
			description: 'The Version of the RAP used to generate the archive.', 
			name: 'productVersion')
			
		choice(choices: "<URL>", 
			description: 'URL of Source Host', 
			name: 'SourceHost')
			
		choice(choices: "<URL>", 
			description: 'URL of Target Host', 
			name: 'TargetHost')		
	}

	//Stages of Pipeline
	stages {

		stage ('Cleanup'){
			steps {
				cleanWs notFailBuild: true
			}
		}

		stage('Git Checkout') {
			steps {
			//Add all the scripts and prpcServiceUtils to this git repo
				git changelog: false, credentialsId: '<credentials>', poll: false, url: '<Git repo ssh URL>'
			}
		}
		//Review the Branch. The branch must be present in the server.
		stage('BranchReview') {
			steps {
				script {
					if (env.branch_name){
						sh 'touch branch-${branch_name}.txt'
						httpRequest outputFile: "branch-${branch_name}.txt", responseHandle: 'NONE', url: "<Server>:<port>/prweb/api/v1/branches/${env.branch_name}/summary"
						sh "cat branch-${branch_name}.txt"
						sh"./branchIsReviewed.py branch-${branch_name}.txt"
						sh "cat branch-${branch_name}.txt"
					}
				}
			}
		}

		/*
		Stage 2: Automated Unit Testing:
		This stage is executed if the test_suite_id parameter is provided or else this stage is skipped
		1. Calls the REST API to execute the Unit Test Suite
		2. The Unit Test Report is stored in the File unitTest${BUILD_TAG}.xml
		
		stage('Automated Unit Tests'){
			steps {
				script{
					if(env.test_suite_id){
						sh "touch $WORKSPACE/unitTest${BUILD_TAG}.xml"
						
						//No authentication included in API call. You can add if you want
						httpRequest httpMode: 'POST', 
						outputFile: "$WORKSPACE/unitTest${BUILD_TAG}.xml", 
						responseHandle: 'NONE', 
						url: "<serverurl>:<port>/prweb/PRRestService/PegaUnit/Rule-Test-Unit-Case/pzExecuteTests?TestSuiteID=${test_suite_id}"
						
						junit '**.xml'
						
						//Abort the build if the Unit Test Suite Fails
						if(currentBuild.result != null){
							System.exit(1)
						}
					}
				}
			}
		} */



		stage('Deploy to L2') {
			steps {
				
				load "${WORKSPACE}/loadprop.properties"
				
				sh 'rm -rf ${WORKSPACE}/tmp'
				sh 'mkdir ${WORKSPACE}/tmp'
				sh "cp ${WORKSPACE}/scripts/samples/jenkins/Jenkins-build.xml ${WORKSPACE}/build.xml"
				
				withAnt(installation: 'Ant_1.10', jdk: 'JAVA_8') {
					sh "ant exportprops"
					sh "${WORKSPACE}/scripts/utils/prpcServiceUtils.sh export --connPropFile ${WORKSPACE}/scripts/utils/${SystemName}_export.properties --artifactsDir $WORKSPACE"
				}
				withAnt(installation: 'Ant_1.10', jdk: 'JAVA_8') {
					sh "ant importprops"
					sh "${WORKSPACE}/scripts/utils/prpcServiceUtils.sh import --connPropFile ${WORKSPACE}/scripts/utils/${SystemName}_import.properties --artifactsDir $WORKSPACE"
				}
			}
		}



		stage('Archive to Nexus') {
			steps {
				sh "cp $WORKSPACE/EXPORT_*/${BUILD_TAG}/${productName}${productVersion}.zip $WORKSPACE"
				nexusArtifactUploader artifacts: [[artifactId: '${productName}${productVersion}', classifier: '', file: '${productName}${productVersion}.zip', type: 'zip']], 
					credentialsId: '<Nexus Credentials>', 
					groupId: '<As per naming convention>', 
					nexusUrl: '<Nexus URL>', 
					nexusVersion: 'nexus3', 
					protocol: 'https', 
					repository: '<repo name>', 
					version: '<Artifact Version>'
			}
		}
	}


	/*post {
		always {
			emailext attachmentsPattern: "unitTest${BUILD_TAG}.xml", 
				body: '''Product: $productName $productVersion, Branch: $branch_name
$DEFAULT_CONTENT''', 
				replyTo: 'nagaraju.b@mphasis.com', 
				subject: '$DEFAULT_SUBJECT', 
				to: 'anil.taduri@mphasis.com'
		}
		//Sending Email at the end of the build
	} */
}
