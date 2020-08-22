pipeline {
	agent none//{
		//docker {
		//	image 'node:13'
		//	label 'akali'
		//	args '-p 3000:3000'
		//}
	//}
	stages {
		stage('BUILD') {
			agent {
				label 'host'
			}
			steps {
				echo "Install dependences and moduler for project"
				sh 'yarn install'
			}
		}
		stage('TEST') {
			when {
				branch 'test'
			}
			agent {
				label 'host'
			}
			steps {
				echo "Test project in development"
				sh 'yarn start & sleep 20'
				echo "webserver on: http://127.0.0.1:3000"
				input message: 'Click to process to allow continue project or abort to quit'
				echo "successful"
			}
		}
		stage('PUBLISH') {
			when {
				branch 'publish'
			}
			agent {
				label 'host'
			}
			steps {
				echo "Build and publish apps to jfrog repo "
				sh 'yarn run build'
				//echo "Test production BUILD in build dir"
				//sh 'cd build'
				//sh 'yarn start & sleep 20'
				//echo "webserver on: http://127.0.0.1:3000"
				//input message: 'Continue or Abort'
				//echo 'production is success for deploy another server in build folder'
				//echo "Successful"
				rtUpload (
				    serverId: 'jfrogserver',
				    spec: '''{
				          "files": [
				            {
				              "pattern": "build/*",
				              "target": "npm-data/node-react/"
				            }
				         ]
				    }'''
				 
				    // Optional - Associate the uploaded files with the following custom build name and build number,
				    // as build artifacts.
				    // If not set, the files will be associated with the default build name and build number (i.e the
				    // the Jenkins job name and number).
				    //buildName: 'holyFrog',
				    //buildNumber: '42'
				)
			}
		}
		stage('DEPLOY') {
			when {
				branch 'deploy'
			}
			agent {
				docker {
					image 'node:13'
					label 'host'
					args '-p 3000:3000'
				}
			}
			steps{
				echo "This is stage Deploy app node-react on Docker container"
				rtDownload (
				    serverId: 'jfrogserver',
				    spec: '''{
				          "files": [
				            {
				              "pattern": "npm-data/node-react/",
				              "target": "."
				            }
				          ]
				    }'''
				 
				    // Optional - Associate the downloaded files with the following custom build name and build number,
				    // as build dependencies.
				    // If not set, the files will be associated with the default build name and build number (i.e the
				    // the Jenkins job name and number).
				    //buildName: 'holyFrog',
				    //buildNumber: '42'
				)
				sh 'yarn install'
				sh 'yarn start & sleep 20'
				input message: 'Click to process to allow continue project or abort to quit'
				echo "successful"
			}
		}

	}
}


//intruction: https://www.jfrog.com/confluence/display/JFROG/Declarative+Pipeline+Syntax