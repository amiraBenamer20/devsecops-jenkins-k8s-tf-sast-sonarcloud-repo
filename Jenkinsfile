pipeline {
  agent any
  tools { 
        maven 'Maven_3_2_5'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=asmbuggywebapp -Dsonar.organization=asmbuggywebapp -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=33e525c21481353ef8d851af5f8965ea252847a6'
			}
        } 
  }
}
