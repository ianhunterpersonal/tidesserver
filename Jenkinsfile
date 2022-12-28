pipeline {

	environment {
		imagename = "ianhunterpersonal/tidesserver"
		registryCredential = 'dockerhub'
		dockerImage = ''
	}

    agent any
    stages {
    
         stage('Setup parameters') {
	         steps {
	             script { 
	                 properties([
	                     parameters([
	                         choice(
	                             choices: ['DEV', 'TEST', 'PROD'], 
	                             defaultValue: 'DEV',
	                             name: 'ENVIRONMENT'
	                         ),
	                         booleanParam(
	                             defaultValue: true, 
	                             description: '', 
	                             name: 'BOOLEAN'
	                         ),
	                         booleanParam(
	                             defaultValue: false, 
	                             description: '', 
	                             name: 'CI'
	                         ),
	                         text(
	                             defaultValue: '''
	                             this is a multi-line 
	                             string parameter example
	                             ''', 
	                              name: 'MULTI-LINE-STRING'
	                         ),
	                         string(
	                             defaultValue: 'scriptcrunch', 
	                             name: 'STRING-PARAMETER', 
	                             trim: true
	                         )
	                     ])
	                 ])
	             }
         	}
         }
            
        stage('Clean') { 
            steps { 
               sh 'echo "Cleaning..."; mvn clean'
            }
        }
        stage('Compile') { 
            steps { 
               sh 'echo "Compile..."; mvn compile'
            }
        }
        stage('Test') { 
            steps { 
               sh 'echo "Testing..."; mvn test'
            }
        }
        stage('Package') { 
            steps { 
               sh 'echo "Testing..."; mvn package'
            }
        }

        stage('Building image') {
			  steps {
			   sh 'echo "Building image...."'
				script {
					dockerImage = docker.build imagename
					echo "${dockerImage}"
				}
			 }
		  }
		  
	    stage('Deploy Image') {
	      steps{
	        script {
	          docker.withRegistry( '', registryCredential ) {
	            dockerImage.push("$BUILD_NUMBER")
	            dockerImage.push('latest')
	          }
	        }
	      }
	    }
	    
	    stage('Remove Unused docker image') {
	      steps {
	         sh "docker rmi $imagename:$BUILD_NUMBER"
	         sh "docker rmi $imagename:latest"
	      }
	    }        
	}
}
