pipeline {
  agent any
  tools { 
        maven 'Maven_3_2_5'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            environment
             {
                SONAR_TOKEN = credentials('sonarcloud') // Use the credential ID here
             }
            steps 
            {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=asmbuggywebapp -Dsonar.organization=asmbuggywebapp -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=${SONAR_TOKEN}'
		}
        }
      stage('RunSCAAnalysisUsingSnyk')
       {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
      }

      stage('Build') 
      { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
                 }
               }
            }
      }

      stage('Push') 
      {
            steps {
                script{
                    docker.withRegistry('https://687447940515.dkr.ecr.eu-west-3.amazonaws.com', 'ecr:eu-west-3:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}

      stage('Kubernetes Deployment of ASG Bugg Web Application')
       {
	   steps {
	      withKubeConfig([credentialsId: 'kubelogin']) {
		  sh('kubectl delete all --all -n devsecops')
		  sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
		}
	   }
   	}

      stage ('wait_for_testing'){
	   steps {
		   sh 'pwd; sleep 180; echo "Application Has been deployed on K8S"'
	   	}
	   }
	   
	stage('RunDASTUsingZAP') {
          steps {
		    withKubeConfig([credentialsId: 'kubelogin']) {
				sh('zap.sh -cmd -quickurl http://$(kubectl get services/asgbuggy --namespace=devsecops -o json| jq -r ".status.loadBalancer.ingress[] | .hostname") -quickprogress -quickout ${WORKSPACE}/zap_report.html')
				archiveArtifacts artifacts: 'zap_report.html'
		    }
	     }
       } 
    
  }
}



