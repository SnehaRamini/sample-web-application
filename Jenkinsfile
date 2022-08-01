
        

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
                    withCredentials([string(credentialsId: 'nexus_pass', variable: 'docker_pass')]) {
                    sh '''
                     docker built -t 54.242.92.101:8083/webapp:${VERSION} .
                     docker login -u admin -p ${docker_pass} 54.242.92.101:8083
                     docker push 54.242.92.101:8083/webapp:${VERSION}
                     docker rmi 54.242.92.101:8083/webapp:${VERSION}
                    '''
                    }
                  }
                }
              }



          
	
		
               }
	       
	       
	       
	      
    
}
