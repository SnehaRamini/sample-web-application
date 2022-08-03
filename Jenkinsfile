
        

pipeline{
         agent any
          environment
          {
            VERSION = "${env.BUILD_ID}"
          }
        stages{
              stage('Quality Gate Status Check'){
		      agent {
                       docker {
                          image 'maven:latest'
                          args '-v $HOME/.m2:/root/.m2'
                              }   
                          }
                  steps{
                    
        
                      script{
                      withSonarQubeEnv('sonar') { 
                      sh "mvn sonar:sonar"
                       }
                      timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
		            sh "mvn clean install"
                  }
                }  
              }

              stage(" build & deploy war file"){
                steps{
                  script{
                  withCredentials([string(credentialsId: 'docker_password', variable: 'dockerpassword')]) {
                    sh '''
                     docker build -t 3.89.106.19:8083/webapp:${VERSION} .
                     docker login -u admin -p $dockerpassword 3.89.106.19:8083
                     docker push 3.89.106.19:8083/webapp:${VERSION}
                     docker rmi 3.89.106.19:8083/webapp:${VERSION}
                    '''
                    }
                  }
                }
              }


        	stage('ansible playbook'){
			steps{
			 	script{
				   
				    ansiblePlaybook become: true, installation: 'ansible', inventory: 'hosts', playbook: 'ansible.yaml'
				}
			}
		}
		
          
	
		
               }
	       
	       
	       
	      
    
}
