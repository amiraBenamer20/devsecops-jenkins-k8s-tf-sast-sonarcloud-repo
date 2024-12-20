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
    
  }
}



